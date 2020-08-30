### **What is it and why do we need it**

* Configure relationships between containers
* Save docker container run settings in one file
* Comprised of 2 seperate, but related things:
  * YAML file that descibes the containers, networks & volumes.
  * The CLI tool

Assignment: Writing your first docker compose for a Drupal CMS along with Postgres

```yaml
version: "3"

services:
    drupal:
        image: drupal:7.69-fpm-alpine
        ports:
            - 1111:80

    postgres:
        image: postgres:10
        environment:
            POSTGRES_PASSWORD: example
```