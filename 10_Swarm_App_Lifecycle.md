# Using Secrets with Local Docker Compose

With docker-compose 11 you can use docker compose file locally (not in a swarm)
- `docker compose up`

Secrets will be mapped automatically (clear text not encrypted) NOTE: only for testing
it does a -v automatically. It works only with text file secrets (not command line)

```yaml
version: "3.1"

services:
  psql:
    image: postgres
    secrets:
      - psql_user
      - psql_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/psql_password
      POSTGRES_USER_FILE: /run/secrets/psql_user

secrets:
  psql_user:
    file: ./psql_user.txt
  psql_password:
    file: ./psql_password.txt
```

# Full App Lifecycle with compose

Interesting video needs to be rewatched again and again

# Service Updates

- Provides **rolling replacement** of tasks/container in a service.
- **Limits** downtime.
- Will **replace containers** for most changes.
- Has many cli option to control the update.
- Includes rollback and healthcheck options.
- Has scale and rollback subcommand for quicker access.
  - `docker service scale web=4` `docker service rollback web`.
- A stack deploy **when pre-existing** stack, this will be an **update**.

## Some examples

Update the image
- `docker service update --image myapp:1.2.1 <servicename>`

Adding an env variable and remove a port
- `docker service update --env-add NODE_ENV=production -publish-rm 8080`

Changing number of replicas of two services
- `docker service scale web=8 api=6`

This is an update
`docker stack deploy -c file.yml <stackname>`

## Docker Healthcheck

- exit 0 (OK) or exit 1 (Error).
- Three container states
  - Starting
  - Healthy
  - Unhealthy
- Not a external monitoring replacement.

`docker container ls`
- Healthcheck is showing up.

`docker container inspect`
- You can see last 5 healthchecks.

Services will **replace** tasks if **unhealty**.
Service update will wait for healtyness before continuing.

### Example

`docker container run --name p2 -d --health-cmd="pg_isready -U postgres || exit 1" postgres`