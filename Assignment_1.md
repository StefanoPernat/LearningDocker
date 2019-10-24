# Assignment I

- Run a **nginx**, **mysql** and **httpd** (apache)
- Run all of them in *--detach* mode and **name** them 
- **nginx** should listen on *80:80*, **httpd** on *8080:80* and **mysql** on *3306:3306*
- when running mysql use *--env* option (or -e) to pass in **MYSQL_RANDOM_ROOT_PASSWORD**=yes
- Use `docker container logs` on mysql container to find out the random password
- Cleen it all up with `docker container stop` and `docker container rm`
- Use `docker container ls` to be sure all is clean

## Solution

- `docker container run -p 80:80 -d --name step_nginx nginx`
- `docker container run -p 8080:80 -d --name step_apache httpd`
- `docker container run -p 3306:3306 -d --env MYSQL_RANDOM_ROOT_PASSWORD=yes --name step_mysql`
- `docker container logs step_mysql` my root password was ouhiaf9ahquuuPahQuiecoilae1eiPis
- `docker container stop 5d8 c32 7cd`
- `docker container rm 5d8 c32 7cd`