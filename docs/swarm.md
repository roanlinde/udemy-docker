### **Docker Swarm Introduction**

Swarm is the built in orchestration clustering withing Docker 

Since Swarm is not active by default, you can enable it by running:

`docker swarm init`

In the event the you have more than more than one network on defined in `ip a`, specify the main network you use, in this case:

```bash
docker swarm init --advertise-addr enp0s3

# output
Swarm initialized: current node (qe1qz7v5n8rm4n16q9dgt10t4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-4rl65f229t816t6sznyc2z93gdjxyti4ji3fklimm6kz5ohkzm-9es80g8s5y3yndl4qlybrd35m 10.0.2.15:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

What has happened is that Docker created a root signing certificate for the Swarm and issued it to the first manager (the docker machine you just enabled swarm on).
It also create join tokens to be used by worker nodes

Check the docker nodes:

``` bash
docker node ls

# output
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
qe1qz7v5n8rm4n16q9dgt10t4 *   manjaro-main        Ready               Active              Leader              19.03.5-ce
```

## **Creating a new Swarm Service**

```bash hl_lines="47"
# create an alpine service
docker service create alpine ping 8.8.8.8

mvgc5gc0pspzv3qqfgi5dwib1
overall progress: 1 out of 1 tasks 
1/1: running   
verify: Service converged 

# list running services
docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
mvgc5gc0pspz        elastic_sinoussi    replicated          1/1                 alpine:latest      

# inspect the service
docker service ps elastic_sinoussi 

ID                  NAME                 IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
njly65bet6yl        elastic_sinoussi.1   alpine:latest       manjaro-main        Running             Running 38 seconds ago                       

# update the service
docker service update elastic_sinoussi --replicas 3

elastic_sinoussi
overall progress: 3 out of 3 tasks 
1/3: running   
2/3: running   
3/3: running   
verify: Service converged 

# view updated service
docker service ps elastic_sinoussi 

ID                  NAME                 IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
njly65bet6yl        elastic_sinoussi.1   alpine:latest       manjaro-main        Running             Running 23 minutes ago                       
dzfqp3o3apkp        elastic_sinoussi.2   alpine:latest       manjaro-main        Running             Running 13 minutes ago                       
tqfyxt625523        elastic_sinoussi.3   alpine:latest       manjaro-main        Running             Running 13 minutes ago                       

# lets remove one of the containers manaully
docker container rm -f elastic_sinoussi.1.njly65bet6ylamk7c6dco53i8

# if we then check the docker service, we will notice that it has already identified a container was killed and automatically spun up a new one
docker service ps elastic_sinoussi 

ID                  NAME                     IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR                         PORTS
kzu8tncudoje        elastic_sinoussi.1       alpine:latest       manjaro-main        Running             Running 24 seconds ago                                 
njly65bet6yl         \_ elastic_sinoussi.1   alpine:latest       manjaro-main        Shutdown            Failed 30 seconds ago    "task: non-zero exit (137)"   
dzfqp3o3apkp        elastic_sinoussi.2       alpine:latest       manjaro-main        Running             Running 24 minutes ago                                 
tqfyxt625523        elastic_sinoussi.3       alpine:latest       manjaro-main        Running             Running 24 minutes ago                                 


# lastly, remove the service
docker service rm elastic_sinoussi



```
