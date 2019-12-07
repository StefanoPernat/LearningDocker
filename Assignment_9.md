# Assignment: Create A Multi-Service Multi-Node Web App

- Using Docker's Distributed Voting App
- Use swarm-app-1 directory in our course repo for instructions

### Networks

- `docker network create --driver overlay backend`
- `docker network create --driver overlay frontend`

### Services

- `docker service create --name db --mount type=volume,source=db-data,target=/var/lib/postgresql/data --network backend --replicas 1 postgres:9.4`
- `docker service create --name redis --network frontend --replicas 1 redis:3.2`
- `docker service create --name worker --network frontend --network backend --replicas 1 bretfisher/examplevotingapp_worker:java`
- `docker service create --name result --network backend --replicas 1 -p 5001:80 bretfisher/examplevotingapp_result`
