Docker 
======
docker command has been update to be in such template:

```
docker [management category] command
```

## Running a container

to run a container:
```bash
docker container run

options:
    -d to detach from the running container (return the temrminal)
    -p to connect ports host: container 
    --name to give a name for each container (optional, if not given, randomly give a name)
    --rm : if used with run command after stop it will be removed
```
To get the list of running or (-a) all containers 

```
docker container ls  (docker ps)
```

To get a shell inside a container in run time:
```
docker container run -it
```
start new container  **interactively** (i: interactive t : pseudo-ttys)
This command should not be used with `-d` (detach command). When the `-it` is used at the end of command we can pass the command we want to run like the shell name (ex. bash , etc)

To satrt a container which already image exist (for exampled stoped before):
```
dokcer container start
```
to connect a shell to already running container (like equavalance of connecting via ssh to a sever )
```
docker container exec -it [container name] [cmd]
```

## Docker Networks:
sometimes we need to open ports (connect to the host), for that:

```
docker container run -p [host:container port]
```
expose the port on container to host
to list all the open ports of a container:
```
docker container port  print all the open ports connections
```

to see the ip addess or all relevant info (configs, settings) on a container, we can sue the `inspect` command. The output json could be parsed with format flag, like

```
dokcer container inspect --format '{{....}}' 
```
where, format used to filter our the output (or just pipe to grep)

To get a list of all netwroks:
```bash
docker network ls : lis all the networks
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
8c21fd40c231        bridge              bridge              local : default network which is created
9141e1d27f02        host                host                local : the main interface of the host,
c0ee13a6ce57        none                null                local: nothing connected
```
or creat a network:
```bash
docker network create blabla
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
e34aa22614e9        blabla              bridge              local
8c21fd40c231        bridge              bridge              local
9141e1d27f02        host                host                local
c0ee13a6ce57        none                null                local
```

similarly the ipect command could be used with network as well to see the configuration of the network
```bash
docker network inspect : 
```

and connect / disconnect to connect a container to a network:

```bash
docker network connect / disconnect
```

containers could be connected to multiple networks. While creating and running a container we could as well specifiy the network with `--network`.

## DNS:

IP addresses are dynamic and could not be relied on for the multiple container to talk with each other. Therfore, the solution is to use DNS, **docker considers the container names as the host name**.

if we want to have multiple containers to respond to same `name` we could use the `network aliases`.

This is an example of creating two container with same `net aliases` on the same network created as blabla

``` 
docker container run -d --net blabla --net-alias search elasticsearch:2
docker container run -d --net blabla --net-alias search elasticsearch:2
```
now if we just run nslookup on another contanier we see both
```
docker container run --rm  --net blabla alpine:3.5 nslookup search
nslookup: can't resolve '(null)': Name does not resolve

Name:      search
Address 1: 172.18.0.2 search.blabla
Address 2: 172.18.0.3 search.blabla
``` 

## Tagging an image 

command :
```
docker image tag src target
```
after tagging the image we could as well push to the docker hub 

```
docker image push
```

## DockerFile and build

DockerFile are the list or configuration of the command to build a container. (instead of for example using a bash script to run all the necessary command)

Some of the usefull command in a DockerFile are:

> **FROM** all image should have a from to start

> **ENV** to set the required env variables

> **RUN** to run a command 

> **EXPOSE** to open ports (sepearet with comma for multiple ports) 

> **CMD** to run a command when container is launched 

> **WORKDIR** like cd to dir

> **COPY** to copy the file/s into image

The orders are important means that it is better to put the least changing steps at the begining, then everytime when small chnages happened so many layers are already saved and caches. Docker to save space always hashes the layers if other modifications it is not changed only the layers affected will be recreated. then better the changing parts to be at the end.

To build from same directory that DockerFile exists, simply run:

``` 
docker image build -t tagname path
``` 

## Docker Volumes

if the container is going to use some data which we want to keep and perserve it is better to be created as volume.
Volumes could be created in Dockerfile 
normally in such cases the volumes are just tags and in run time could be given as named-volumes

for exmaple mysql image has a volume defined in its docker file, (you could check this by looking into image inspect ( /var/lib/mysql) 
if not specified every time at run it will create a new volumes and asign to container which lives even after container removed (for security), "data persist".

if the container runned like this the volume could be used if later other container are created (reused the same volume)
```
docker container run -d -e MYSQL_ROOT_PASSWORD=my-secret-pw  -v test:/var/lib/mysql mysql
```

now like this docker recongize the test as named-volume and coule be reused in others container (for examle if container updated). list of all volumes as well could be seen with `docker volume ls`

## Bind Mounting
similar to volumes, there are possibility to map host location to containers spaces. This is done similar to named-volumes. Just the difference is that the path in host is given instead of name. docker knows the difference by starting claracter. If it starts with / or in windows // then it is a path and it is bind mounting.
```
run -v /path to host location:/path/in/container
```

## Docker-compose

Similar to dockerfile for building an images, we could use compose files for multi-containers. docker-compose. yml files are used for defining and running multi-container Docker applications, whereas Dockerfiles are simple text files that contain the commands to assemble an image that will be used to deploy containers. services are actually the containers here.

example: 

```yml 
version: '2' 
services:
    drupal:
        image: custom-drupal # name is not important here, could be skipped  
        build: . # this means just build the image from the same dir that has a DockerFile. 
    #The "." indicates the dockerfile exists in the same directory as the compose file. 
        ports:
            - "8080:80" 
        volumes:
            - drupal-modules:/var/www/html/modules 
            - drupal-profiles:/var/www/html/profiles 
            - drupal-sites:/var/www/html/sites 
            - drupal-themes:/var/www/html/themes 
    postgres:
        image: postgres:12.1 
        environment:
            - POSTGRES_PASSWORD=mypasswd 
        volumes:
            - drupal-data:/var/lib/postgresql/data 

volumes:
drupal-data:
drupal-modules:
drupal-profiles:
drupal-sites:
drupal-themes:

```


