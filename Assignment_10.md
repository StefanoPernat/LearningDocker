# Create Stack with Secrets

## Starting from previous compose file

```yaml
version: '3.1'

services:

  drupal:
    image: drupal:8.2
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles       
      - drupal-sites:/var/www/html/sites      
      - drupal-themes:/var/www/html/themes
 
  postgres:
    image: postgres:9.6
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      - drupal-data:/var/lib/postgresql/data

volumes:
  drupal-data:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:

secrets:
  psql-pw:
    external: true
```

- Changed image to `drupal:8.2`
- Removed build
- Added password file to postgres
  - Declared secret inside postgres
  - Declared secret as external
- Created secret via cli 
  - `echo <psw> | docker secret create psql-pw -`
- `docker stack deploy -c docker-compose.yml drupal`
  - Run on node 1