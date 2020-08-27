# Docker Security
see this list : https://github.com/bretfisher/ama/issues/17#issuecomment-400163774


1. Use docker CGropus and namespaces
  namespace is the feature of linux: give the possibility to kernel to assign the app to certain name space with limited access /view (filessytem, netwroking , ...)
  Control groups: used to define limitation for application (memory, storage and etc)
2. Docker Engine : Check the image 
  Dont use random images just tructed images
  Docker itself has some security feature out of the box to limit the privilages of the apps inside (app armour, SElinux , ..)
 3. Docker Bench
  Run script inside the container to check and scan for correct configuration, and gives a report of the features
 4. Using USER in dockerfile
  if posible do not run apps as root inside the container. you can create new user and scripts/code etc could be run as the user not the root. (root inside container normally should have access to anything on the host)
  
 5. Docker user namespaces
  not enabled by default, could be configured in docker configuration json file to run the containers not as root
  
 6. Code Repo Scanning
 - Using tools like sneak to scan the repos (guthub, bitbucket, etc) for dependancies and valunerabilties
 - using tools like tivy for scanning the image for CVE vulnerabilities (databases , open source apps)
 
 7. sysdif Falco, content trust
 - tools like Falco runs on the server and audit al the activity of the containers for suspicious behaviour, (bind mounting sesnitive data, accessing, writing to suspicious places and etc)
 - these tools come with default set of ruls for bad actions but as well new rules could be added too
 
 8. docker rootless
 - you cant create networks, bridge and etc, since it needs root in linux
- see here: http://get.docker.com/rootless
 
