# Build and run compose

This time we will build and run compose from a Dockerfile

We have to follow intructions from README file, we have to
write a Dockerfile and a docker-compose.yml

## Dockerfile

```dockerfile
FROM drupal:8

RUN apt-get update && \
    apt-get install -y git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /var/www/html/themes

RUN git clone --branch 8.x-3.x --single-branch --depth 1 \
    https://git.drupal.org/project/bootstrap.git && \
    chown -R www-data:www-data bootstrap

WORKDIR /var/www/html
```
## docker-compose.yml

```yaml
version: '2'

services: 
  drupal:
    image: custom-drupal
    build: .
    ports: 
      - '8080:80'
    volumes: 
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-themes:/var/www/html/themes
      - drupal-sites:/var/www/html/sites
    restart: always

  postgres:
    image: postgres:10
    environment: 
      POSTGRES_PASSWORD: root
    volumes: 
      - drupal-data:/var/lib/postgresql/data
    restart: always

volumes: 
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-themes:
  drupal-sites:
```