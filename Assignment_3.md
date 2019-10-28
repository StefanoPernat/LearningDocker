- Create a new virtual network (default bridge driver)
- Create two containers from **elasticsearch:2** image
- Research and use *-network-alias search* when creating them to give them
to give them an additional DNS nameto respond to
- Run `alpine nslookup search` with `--net` to see the two containers 
list for the same DNS name
- Run `centos curl -s search:9200` with `--net` multiple times until you see
both "name" fields show

Ever since Docker Engine 1.11 we can have multiple containers on a created network
respond to the same DNS address

# Solution

- `docker network create search_net`
- `docker container run -d --name search_1 --network search_net --network-alias=search elasticsearch:2`
- `docker container run -d --name search_2 --network search_net --network-alias=search elasticsearch:2`
- `docker container run --rm --network search_net alpine nslookup search`
- `docker container run --rm --network search_net curl -s search:9200` - until name changes