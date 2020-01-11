# Container Registries

- An image registry needs to be part of your container plan.
- More docker hub details and auto-build.
  - How docker store is different than Hub.
  - How docker cloud is different than Hub.
- Install and use Docker Registry as a private image store.
- 3rd party registry option.

## Docker Hub

- The most pupular public image registry.
- Docker registry + lightweight image building.

## Running Docker Registry

- A private image registry for your network
- Part of the docker/distribution GitHub repo
- The de facto in private container registries
- Not as fully featured as DockerHub, or others, no web UI, basic auth only
- Web API and storage system 
- Storage support local, S3, Azure, Alibaba, Google Cloud, Open Stack Swift

## Run a private Docker Registry

`docker container run -p -d 5000:5000 --name registry registry`

- We have to tag an image we want to use in our local registry with the host
  name

`docker tag hello-word 127.0.0.1:5000/hello-word`