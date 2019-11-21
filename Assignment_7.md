# Assignment VII

How to write a basic Compose file

## Instruction

1. Build a basic **Compose** file for **Drupal CMS**.
2. Use a **Drupal image** along with **Postgres image**.
3. Read through docs.
4. Expose drupal on **8080**.
5. Set *POSTGRESS_PASSWORD* for postgres
6. Drupal assumes db is at localhost but set it to service name
7. Use volumes to store drupal unique data

## My **drupal-compose.yml**

```yaml
version: '2'

services: 
  drupal:
    image: drupal
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
    restart: always

volumes: 
  drupal-modules:
  drupal-profiles:
  drupal-themes:
  drupal-sites:
```

## Notes

- postgres image needs to be `postgres:10` otherwise a crah happends