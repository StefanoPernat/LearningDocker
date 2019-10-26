# Docker Networks

When you start a container you are connecting to a particular docker
network
- Each container is connected to a private virtual network *"bridge"*
- Each virtual network routes through NAT firewall on host IP 
(docker deamon configuring on his default interface the host IP address) so
containers can get out to internet or the rest of your network
- All containers on a virtual network can talk without -p
* Best practice is to create a new virtual network for each app
    - network *"my_web_app"* for **mysql and PHP/apache containers**
    - network *"my_api"* for **mongodb and nodejs containers**

## Useful Commands

`docker container run -p 80:80 -d --name webhost nginx`

- `docker container port webhost` - shows port mappings 
- `docker container inspect webhost` - shows container configuration as JSON
- `docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost` - is possible to filter configs using Go templates like this
- My docker container has ip 172.17.0.2 (Virtual network)

# CLI management of virtual networks

- `docker network ls` - shows docker network
- `docker network inspect` - shows detail about networks
- `docker network create --driver` - it allows to create virtual networks
- `docker network connect` - attach a network to a container
- `docker container disconnect` - detach a network from container
- `docker network inspect` - show network configuration

`docker network ls`

NETWORK ID  |NAME  |DRIVER|SCOPE|
------------|------|------|-----|
a3454844c024|bridge|bridge|local|
0a1274f5c62f|host  |host  |local|
f9a99e1b8a1b|none  |null  |local|

> bridge is the default docker virtual network that is 
> NATed behind the host IP

> network named host attaches the container directly to the
> host interface, and skips docker virtual networking gaining 
> speed but sacrifices security

> network named none is equivalent to have an interface on my
> pc that's attached to anything, removes eth0 from container and
> it leaves only with localhost

`docker network create my_app_net`
`docker container run -d --name new_nginx --network my_app_net nginx`
`docker network connect bbfed8951876 0dc369127ccc`
`docker network disconnect bbfed8951876 0dc369127ccc`

# Default Security

- Create your app so frontend/backend sit on the same Docker network
- Their inter-communication never leaves host
- All externally exposed ports closed by default
- You must manually expose via -p which is better default security

# DNS

- Understand how DNS is the key to easy inter container communication
- See how it works by default with custom networks
- Learn how to use --link to enable DNS on default bridge network


- Docker defaults the hostname to the container's name but aliases can be used
- new networks created with docker has DNS enabled so containers can talk to
  each other regardless IP address

`docker container run -d --name my_nginx --network my_app_net nginx:alpine`

> used alpine version because it has ping installed

`docker network inspect my_app_net`
```json
"Containers": {
    "24686d79764892e057a4ad639de1ca30d8abad83a3027afc963a0effee191b65": {
        "Name": "my_nginx",
        "EndpointID": "9688b39c3d31839159e9bc95df6d800d6911036c4534414ea6b00943116e0f2a",
        "MacAddress": "02:42:ac:12:00:03",
        "IPv4Address": "172.18.0.3/16",
        "IPv6Address": ""
    },
    "f68dc227b749d60f4efaee7f6945498d0d29d893d26930e422d8f0432477c4fa": {
        "Name": "new_nginx",
        "EndpointID": "14aa23d694264e0a3920235c55f76e0c22e9cc43892f091fe953ceb8f9af7590",
        "MacAddress": "02:42:ac:12:00:02",
        "IPv4Address": "172.18.0.2/16",
        "IPv6Address": ""
    }
}
```


```sh
docker container exec -it my_nginx ping new_nginx
PING new_nginx (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.213 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.185 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.175 ms
```