# Notes for Docker and Kubernetes

docker command has been update to be in such template:

```
docker [management category] command
```

## Running a container

to run a container:
```
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
```
docker network ls : lis all the networks
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
8c21fd40c231        bridge              bridge              local : default network which is created
9141e1d27f02        host                host                local : the main interface of the host,
c0ee13a6ce57        none                null                local: nothing connected
```
or creat a network:
```
docker network create blabla
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
e34aa22614e9        blabla              bridge              local
8c21fd40c231        bridge              bridge              local
9141e1d27f02        host                host                local
c0ee13a6ce57        none                null                local
```

docker network inspect : to see the configuration of the network
docker network connect / disconnect to connect and disconnect a container to a  network , container could be connected to multiple networks
while creating an running a container we could specifiy the network as well with --network



DNS:
ip addresses are dynamic and could not be relied on for the multiple container to talk with each other, to this the solution is to use DNS,
docker considers the container names as the host name

if we want to have multiple containers to respond to same name we could use the network aliases
for example creating two container with same net aliases ont he same network created as blabla

docker container run -d --net blabla --net-alias search elasticsearch:2
docker container run -d --net blabla --net-alias search elasticsearch:2

now if we just run nslookup on another contanier we see both

docker container run --rm  --net blabla alpine:3.5 nslookup search
nslookup: can't resolve '(null)': Name does not resolve

Name:      search
Address 1: 172.18.0.2 search.blabla
Address 2: 172.18.0.3 search.blabla



Tagging an image 

command :
docker image tag src target
after tagging the image we could as well push to the hub 
docker image push


DockerFile and build
command are 


FROM all image should have a from to start
ENV to set the require env variables
RUN to run the command 
EXPOSE the port 
CMD run this command when container is launched 
WORKDIR like cd to dir
COPY to copy the file into image
The order are important means better to put the least changing steps are the begining, then everytime when small chnage happened som many layers are already saved and caches

to build from sam directory that DockerFile exist run 
docker image build -t tagname path

Docker Volumes

if the container is going to use some data which we want to keep and perserve it better to be created as volume
Volumes could be created in Dockerfile 
normally in such cases the volumes are just tags and in run time could be given as name-volumes

for exmaple mysql image has a volume defined in its docker file, (you could check this by looking into image inspect ( /var/lib/mysql) 
if not specified every time at run it will create a new volumes and asign to container which lives even after container removed (for security)

if the container ran like this the volume could be used if later other container are created (reused the same volume)

$ docker container run -d -e MYSQL_ROOT_PASSWORD=my-secret-pw  -v test:/var/lib/mysql mysql



Bind Mounting
similar to volumes there are possibility to map host location to containers spaces
this is done similar to named volume just this time it starts with / or in windows //
run -v /path to host location:/path/in/container



docker-compose
example: 
version: '2' 
# NOTE: move this answer file up a directory so it'll work

services:

drupal:
image: custom-drupal # Just name anything , could be skipped  
build: . # this means just build the image from the same dir that has a DockerFile 
The "." indicates the dockerfile exists in the same directory as the compose file. 
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



Docker Swarm

by default it is not active, 
docker swarm init


Stacks 
To write the silimar to compose files for the swarms
yml files should be starting version 3 to support stack

secrets:

normally for storing the password we could give these with —secret option which create ramfs files inside the container which is accessable

if needed to be read from the file append _FILE to the end and give the path to the txt file (instead of cat)

when included on the yml files, version should be minimum 3.1 to support secrets

