# X11 forwarding 

most of the time, specially while using the docker as a evaluation setup for the data science task, it is needed to forward the display from the machine running in the container. like in the example of plotting the data. 

Depending on the host machine this could be different 

## On a Mac OSx Host

Steps are as follow

1. Install [XQuartz](https://www.xquartz.org) as the x server on Mac
2. Run xterm. To verify now the `$DIPSLAY` envirounemt variable should be set to an address like (/private/tmp/com.apple.launchd.xxxx/org.macosforge.xquartz:0)
3. run :
`xhost + 127.0.0.1` 
to add the local net to the access control list

3. Now set the DISPLAY in the run command to pass the display address to the container:
````
docker run --rm -it -e DISPLAY="host.docker.internal:0" pytorch/pytorch
````


## Linux as a host

The steps are similar, just there is no need to install x server (normal installation come with)

1. Run the command:
`xhost +local:root`
to add the local connections to acees control list of x server

2. now while runninf the image display envirounement and network should be set `-e DISPLAY=$DISPLAY --net="host"`
````
docker run  --rm -it -e DISPLAY=$DISPLAY --net="host" ubuntu
````

## Windows as a host

Similar steps, just recommened to install [vcxsrv](https://github.com/ArcticaProject/vcxsrv) (instead of xming)
1. then run the xhost +local:root on host
2. now either pass the DISPLAY envirounemt variable as [host-ip]:0.0 or set it inside the container


### ssh x11 forwarding problem on windows:

In windows setting the DISPLAY env make a problem with ssh_askpass, the workarounf would be to create an object on C (\dev\tty)
mkdir \dev
echo x > \dev\tty
then if the DiSPLAY is set on windows side (setx DISPLAY=localhost:0.0) ssh -Y option should work.
