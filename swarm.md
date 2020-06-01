## Docker Swarm

by default it is not active, to activate:
```
docker swarm init
```

### Stacks 
To write the silimar to compose files for the swarms.
yml files should be starting at least with `version 3` to support stack.

secrets:

normally for storing the passwords we could give these with `--secret` option which create ramfs files inside the container which is accessable to container.

if needed to be read from the file append `_FILE` to the end of varible name and give the path to the txt file (instead of cat)

when included on the yml files, version should be minimum 3.1 to support secrets.
