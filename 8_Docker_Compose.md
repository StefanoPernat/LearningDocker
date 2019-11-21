# Docker Compose

- Configure relationship between containers
- Save our docker container run settings in **easy-to-read** files
- Only expose pubblic ports
- Create one-line developer enviroment startups

*Docker Compose* can be divided in two related things

1. YAML formatted file that describes our solution for:
    - container
    - network
    - volumes

2. A CLI tool `docker-compose` used for local dev/test automation with those 
   YAML files

## docker-compose.yml

- Compose YAML format has its own version: 1,2,2.1,3,3.1
- Used with:
  - `docker compose`.
  - With `docker` directly in production with **Swarm**.
- **docker-compose.yml** is the standard file name but can be customized
  with `docker-compose -f`

Example: 

```yaml
version: '3.1'  # if no version is specificed then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```

## docker-compose CLI

- CLI tool comes with docker on Windows/Mac but separate on Linux
- Not a production grade tool but ideal for local development and test
- Most common commands
  - `docker compose up` setup volumes/networks and start all containers
  - `docker compose down` stop all containers and removes container, volumes
  and networks
- If your project has a **Dockerfile** and **docker-compose.yml** than a new developer can
  do:
  - `git clone github.com/some/software`
  - `docker-compose up` (remember to do `docker compose down` also)

### Example

```yaml
version: '3'

services: 
  proxy:
    image: nginx:1.13     # it uses latest version of nginx:1.13.x
    ports: 
      - '80:80'           # from port 80 on host to port 80 in container
    volumes: 
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      # volumes that maps nginx.conf to default.conf in read only mode
  web:
    image: httpd          # is like httpd:latest
```