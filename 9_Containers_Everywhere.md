# Containers Everywhere = New Problems

- How do we automate container lifecycle?
- How can we easily scale out/in/up/down?
- How can we ensure our containers are re-created if they fail?
- How can we replace container without downtime?
- How can we control/track where containers get started? (which node)
- How can we create cross node virtual networks?
- How can we ensure only trusted servers run our containers?
- How can we store secrets, keys, passwords and get them to the right container?
  (and only that one)

## Swarm mode: Built-In Orchestration

- **Swarm mode** is a clustering solution built inside Docker.
  - It brings together different operating system or host
    into a single manageable unit that you can orchestrate the lifecycle
    of your containers in.
- Not related to **Swarm classic** pre 1.12 version.
- Not enabled by default
- **Manager Nodes**: they have a database locally known as the raft database
  - it stores their configuration and gives them all info they need to have
    to be the authority inside a swarm (Manage the swarm securely).
    - Manages is tipically a **Worker with permission** to control the swarm.
- **Worker nodes**
- **Raft** consensus database is replicated among all the nodes.
- You can demote and promote **workers** and **managers**.

## Swarm Services: Create your first services

`docker swarm init` initialize a swarm (if inactive this is mandatory)

### What happened

#### Security automation

- It creates a **root signing certificate** for the swarm.
  - used to establish trust and sign certificate for all nodes and all managers
- Certificate _is issued for first manager node_.
- **Joins tokens** are created.
  - we can use these tokens on other nodes to join this swarm.

#### Raft database

- Is created to **store root CA**, **configs** and **secrets**
- Encrypted by default on disk (1.13+).
- **Replicate logs** amongst Managers via mutual TLS "control plane".

`docker node ls`
`docker service COMMAND` in a swarm docker service replaces docker run

`docker service create alpine ping 8.8.8.8`
`docker service ls` list all services

|ID         |NAME             |MODE      |REPLICAS|IMAGE        |PORTS|
|------------|-----------------|----------|--------|-------------|-----|
|xx9v1g95bp80|objective_davinci|replicated|1/1     |alpine:latest|     |

`docker service ps objective_davinci` list all container associated with service

|ID          |NAME               |IMAGE        |NODE          |DESIRED STATE|CURRENT STATE        |ERROR|PORTS|
|------------|-------------------|-------------|--------------|-------------|---------------------|-----|-----|
|0kc2zvw36tng|objective_davinci.1|alpine:latest|docker-desktop|Running      |Running 7 minutes ago|     |        |

`docker container ls` still works adding extra info to the name
`docker service update xx9v1g95bp80 --replicas 3` updates a service
`docker service rm [NAME or ID]` removes a services

## Creating 3-Node Swarm: Host Option

Create 3 droplet on Digital Ocean
- `docker swarm init --advertise-addr 167.172.215.243`

On the other 2 droplets
- `docker swarm join --token SWMTKN-1-3iqrwydeot1qeiof9x4izvggkydz4ps9hfovhw50pls42juqnr-9ueax7zt7zkxe966jm0wv9c9s 167.172.215.243:2377`

To upade a node as **manager**
- `docker node update --role manager node2`
- (Alternative) `docker swarm join --token SWMTKN-1-3iqrwydeot1qeiof9x4izvggkydz4ps9hfovhw50pls42juqnr-dntau573k5rgbfe6yp67ac314 167.172.215.243:2377`

## Overlay Multi-Host Networking

- New network driver named **overlay** when creating network
  - Is basically a Swarm wide bridge network where the containers across hosts on the same
    virtual network can access each other like they are on a VLAN.
  - Only for container to container traffic inside a Swarm
  - Optional IPSec (AES) encryption on network creation.
- Each service can be connected to multiple networks
  - (Front-end, Back-end)

## Scaling Out with Overlay Networks

`docker network create --driver overlay mydrupal`
- create an overlay network

`docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=pass postgres`
- create service **psql** on network **overlay**

`docker service create --name drupal --network mydrupal -p 80:80 drupal`
- other service

## Routing Mesh

- **Routes** ingress (incoming) packets for a service to proper Task.
- **Spans all the nodes** in Swarm.
- Uses IPVS from Linux Kernel.
- **Load balances** Swarm Services across their Tasks.
- Two ways
  - Container-to-container in an **overlay** network, uses Virtual IP (previous example).
  - External traffic incoming to published ports (all nodes listen).

`docker service create --name search --replicas 3 -p 9200:9200 elasticsearch`

- This is stateless load balancing
  - OSI Layer 3 (TCP) load balancer not Layer 4 (DNS)
- Both Limitations can be overcome with
  - Nginx, HAProxy, LB proxy or:
    - Docker enterprise edition

## Swarm Stacks: Production Grade Compose

- Basically is a **compose file** for production of Swarm.
- Stacks accept compose files as their **declarative definition** for _services_,
  _networks_ and _volumes_.
- We will use `docker stack deploy` rather than `docker service create`.
- Stack manages all those object for us, including overlay network per stack. Adds
  stack name to start of their name.
- New `deploy` key in compose file, when we are working with stack we can't use `build`.
- Compose ignore `deploy` and Swarm ignore `build`.
- **compose-cli** not needed on Swarm server.

```yaml
version: "3.1"

services:
  redis:
    image: redis:alpine
    ports: 
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]
  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure
  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:
```

## Secret Storage for Swarm

- Esiest "secure" solution for **storing secret** in Swarm.
- What is a secret?
  - **Usernames and Passwords**
  - **TLS certificates and keys**
  - **SSH keys**
  - Any data you would prefer not be on "_front page of news_"
- Support generic **strings or binary content** up to **500Kb** of size
- Doesn't require app to be rewritten
- Secrets are **first stored in Swarm**, then **assigned to a Service**(s)
- Only containers in assigned Services can see them
- They look like files in container but are actually in-memory a RAM file system
  - `/run/secrets/<secret-name>` or `/run/secrets/<secret-alias>`
- Local docker-compose can use file-base secrets but not secure

### Example 

`docker secret create psql_user psql_user.txt`
- passing a file

`echo "myDBpassWORD" | docker secret create psql_pass -`
- command line method

`docker secret ls`

`docker secret insoect psql_user`

`docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres`
- create a service manually using secrets

## Using Secrets with Swarm Stacks

`docker stack deploy -c docker-compose.yml mydb`

```yml
version: "3.1"

services:
  psql:
    image: postgres
    secrets:
      - psql_user
      - psql_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/psql_password
      POSTGRES_USER_FILE: /run/secrets/psql_user

secrets:
  psql_user:
    file: ./psql_user.txt
  psql_password:
    file: ./psql_password.txt
```