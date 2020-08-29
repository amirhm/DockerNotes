### Forwarding the display


To forward the x11 display give the host display as an envirounement variable and as well the netwrok

````
docker run --rm -it -e DISPLAY=$DISPLAY --net="host" pytorch/pytorch
````

and enable the xhost local

````
xhost +local:root
````


### To enbale the GPU


install the nvidia toolkit : link(add here)

then:

````
docker run -it --rm --gpus all -e DISPLAY=$DISPLAY --net="host" pytorch/pytorch bash
````
