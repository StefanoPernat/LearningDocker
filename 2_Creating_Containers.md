# Creating and using container like a boss

## Learn Basic Commands

- `docker version` (check version if docker is installed correctly)
- `docker version` (info about containers and docker)
- `docker container run` (always start a new container)
- `docker container start [id]` (start a stopped container)
- `docker container ls` (list all running containers)
- `docker container stop [id]` (stop the running container specified by id)
- `docker container logs [name]` (if container is running in background it allows to see logs)
- `docker container top [name]` (Display the running process of a container)
- `docker container rm [id]` (Remove the container specified by id)

> Docker commands format is like
>
> docker [command] [sub-command] (options)

## Image vs Container

- An **Image** is an application we want to run (binary, libraries and source code)
- A **Container** is an instance of that image running as a process

We can have many containers running off the same image

## First container

`docker container run --publish 80:80 nginx`

- Download *nginx* image from Docker Hub
- Started new container based on that image
- Opened port 80 on local machine
- Routes the traffic to the container IP port 80

`docker container run --publish 80:80 --detach nginx`

- Runs the container in background

`docker container ls`

CONTAINER ID|IMAGE|COMMAND               |CREATED      |STATUS      |PORTS             |NAMES          |
------------|-----|----------------------|-------------|------------|------------------|---------------|
bb4b0b10db3a|nginx|"nginx -g 'daemon of…"|4 minutes ago|Up 3 minutes|0.0.0.0:80->80/tcp|elastic_swanson|

`docker container stop bb4`

`docker container ls -a`

- Shows all containers not only running ones

`docker container start [id]`

`docker container run --publish 80:80 --detach --name webhost nginx`

- a name can be specified if you don't want a random one

`docker container logs webhost`

`docker container top webhost`

`docker container rm bb4 16f`

`docker container rm -f 41b`

- Force the remove of a container