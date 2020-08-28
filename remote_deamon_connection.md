# Docker contexts

Using docker context it is possiblle to direcly connect to the deamon of the docker on other machine and control it.

Old way: we can define the DOCKER_HOST env and then give the docker commands like this :

by default we have the endpoint unix://var/run/docker.sock which is to connect localy
we could add the tcp port for docker deamon to listen to specific ip ```192.168.59.106:2375``` or all ips ```0.0.0.0```  

````
$ sudo dockerd -H unix:///var/run/docker.sock -H tcp://192.168.59.106 -H tcp://10.10.10.2
````

now on remote we could path this as an env to connect to the remote deamon
```
~ DOCKER_HOST="tcp://xxxx:port" docker ps
```

On newer versions we have the possibility to use the ssh connections to replace this:

````
 docker -H ssh://user@ip:port run  --rm -it ubuntu bash
````

in both cases these endpoint configs could be added to docker contexts:
[all these are true for the kubernetes as well]
````
docker context create --docker "host=tcp://..." [conext_name]
or 
docker context create --docker "host=ssh://u@ip" name
````
and after we could use the `docker context ls` to see all the context and `docker context use [context name ]` to switch to the available deamons

to overwrite the context we can use the `-c` or `--context`
```
docker -c default ps
or 
docker -c home ps



```



