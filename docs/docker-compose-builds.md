
## **Adding custom images to docker-compose files**

For this example we will create a custom dockerfile which builds a drupal image. 
This build file will then be included into our docker-compose file in order to spin it up

```dockerfile
FROM drupal:8
RUN apt-get update && apt-get install -y git \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www/html/themes
RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git \
    && chown -R www-data:www-data bootstrap
WORKDIR /var/www/html
```

Then add this docker file to docker-compose

```yaml
version: "2"

services:
    drupal:
        image: custom-drupal
        build: .
        ports:
            - 8080:80
        volumes:
            - modeuls:/var/www/html/modules
            - profiles:/var/www/html/profiles
            - themes:/var/www/html/themes
            - sites:/var/www/html/sites

    postgres:
        image: postgres:latest
        environment:
            - POSTGRES_PASSWORD=example
        volumes:
            - drupal-data:/var/lib/postgresql/data

volumes:
    modeuls:
    profiles:
    themes:
    sites:
    drupal-data:
```