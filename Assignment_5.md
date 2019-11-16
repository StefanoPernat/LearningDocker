**Assignment Name**: 	_Named Volumes_

**Context**:		Database Upgrades with Containers

Instructions:		
- Create a postgres container with named volume psql-data using version 9.6.1
- Use Docker Hub to learn VOLUME path and version needed to run
- Check logs and stop container
- Create a new postgres container with same named volume using version 9.6.2
- Check logs to validate

Steps:		
- Checking on DockerHub 
  - on docker run enviroment variable POSTGRES_PASSWORD needs to be set
  - default postgres user is postgres can be set via POSTGRES_USER
  - volume directory should be at /var/lib/postgresql/data

- `docker container run -d --name postgres -e POSTGRES_PASSWORD=root -v psql-data:/var/lib/postgresql/data postgres:9.6.1`
- `docker container logs postgres`
- `docker container stop postgres`
- `docker container run -d --name postgres -e POSTGRES_PASSWORD=root -v psql-data:/var/lib/postgresql/data postgres:9.6.2`
- `docker container logs postgres` 
