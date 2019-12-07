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