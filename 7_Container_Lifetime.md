# Container Lifetimes and Persistent Data

- Containers are usually *immutable* and *ephemeral* (unchanging and temporary)
- **Immutable Infrastrucure**: only re-deploy containers, never change once they are running
- What about database, or unique data
- Docker gives us features to ensure this "separation of concerns".
- Solutions are **Volumes** and **Bind Mounts**.
  - **Docker Volumes** are a configuration option for a container that creates
  a special location outside of that container's UFS (Union File System)
  to store unique data.
  - **Bind Mounts** sharing or mounting a host directory or file into a container

## Persisistent Data: Volumes

- `VOLUME` commands in **Dockerfile**
- Volumes needs manual deletion

## Useful commands

- `docker pull mysql` download mysql:latest image
- `docker volume prune` deletes all unused volumes
- `docker image inspect mysql`
- `docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql`

`docker container inspect mysql`

```json
.....
"Mounts": [
    {
        "Type": "volume",
        "Name": "c1cc819685ee53215a22a9453074bdbbae36675d4ff3f7babc94ed5611063249",
        "Source": "/var/lib/docker/volumes/c1cc819685ee53215a22a9453074bdbbae36675d4ff3f7babc94ed5611063249/_data",
        "Destination": "/var/lib/mysql",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
.....
```
- `docker volume ls`

`docker volume inspect c1cc819685ee53215a22a9453074bdbbae36675d4ff3f7babc94ed5611063249`

```json
[
    {
        "CreatedAt": "2019-11-11T20:00:14Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/c1cc819685ee53215a22a9453074bdbbae36675d4ff3f7babc94ed5611063249/_data",
        "Name": "c1cc819685ee53215a22a9453074bdbbae36675d4ff3f7babc94ed5611063249",
        "Options": null,
        "Scope": "local"
    }
]
```

`docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql`

Creates a container with a named volume

- `docker volume create` used when you need to create a volume ahead of time

## Bind Mounting

- Maps a hostfile or directory to a container file or directory
- Just two location pointing the same file(s)
- Skips UFS (Union File System) and host files overwrite any container
- **Can't use in Dockerfile**, must be at `container run`
- `docker container run -v host path: container path`

`docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx`