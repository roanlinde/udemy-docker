## **Using Named Volumes in Upgrades**

### **Basic Idea**
We want to upgrade Postgres from 9.6.1 to 9.6.2 but retail all the data. In order to do this we will have a named volume which we will reassign to the new container

Lets define a few things:

1. `name` = first-postgres
2. `password` = mysecretpassword
3. `database` = roan
4. `volume` = ../psql-data
5. `image` = postgres:9.6.1-alpine

```bash
docker run --name first-postgres -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=roan -v /home/roan/Documents/docs/udemy-docker/psql-data:/var/lib/postgresql/data -d postgres:9.6.1-alpine
```

Once the container has been created, connect to the container and log into postgres by running:

```bash
docker exec -it first-postgres /bin/sh
psql -U postgres

# confirm database version
postgres=# select version();
--------version---------                      
 PostgreSQL 9.6.1 on x86_64-pc-linux-gnu, compiled by gcc (Alpine 6.2.1) 6.2.1
(1 row)


# list all databases 
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 roan      | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
...

# use database named roan and check table structure
postgres=# \c roan
You are now connected to database "roan" as user "postgres".
roan=# \d
No relations found.

# create table
roan=# create table testing (name varchar(50));
CREATE TABLE
roan=# \d
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | testing | table | postgres
(1 row)
```

Now, we have a database with some changes made. Next is to stop the container, remove it, replace it with a newer version and link it to the old data

We will use the same `docker run` command as above, but just up the version and change the name to `second-postgres`

```bash
# first stop the container
docker stop first-postgres
docker rm first-postgres

docker run --name second-postgres -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=roan -v /home/roan/Documents/docs/udemy-docker/psql-data:/var/lib/postgresql/data -d postgres:9.6.2-alpine

docker exec -it second-postgres /bin/sh
psql -U postgres

# check version

postgres=# select version();
--------version--------
 PostgreSQL 9.6.2 on x86_64-pc-linux-musl, compiled by gcc (Alpine 6.2.1) 6.2.1
(1 row)

# confirm the table created above is still there

postgres=# \c roan
You are now connected to database "roan" as user "postgres".
roan=# \d
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | testing | table | postgres
(1 row)

```