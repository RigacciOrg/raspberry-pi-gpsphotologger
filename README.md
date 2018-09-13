# raspberry-pi-gpsphotologger
Python script to take georeferenced photos on a Raspberry Pi, using raspistill and gpsd.

This script acquires georeferenced photos on a Raspberry Pi. 
Photos are taken by an instance of the **raspistill** program, 
which must be running with the "--signal" parameter. GPS 
position is provided by a running **gpsd* instance.

System time must be syncronized with the GPS time, so that the 
exact position of the captured photo will be interpolated by 
reading subsequent positions coming from the GPS.

# How to launch a raspistill instance

The gpsphotologger script must find a running instance of the 
**raspistill** program (provided by the Raspbian 
**libraspberrypi-bin** package), otherwise it cannot take a 
photo from the Raspberry Pi camera. This choiche was made 
because we wish to abstract all the issue concerning camera 
interface, calibration, etc. If raspistill is able to take a 
photo, then gpsphotologger can take georeferenced photos.

raspistill must be running in the background with the --signal 
option, i.e. waiting for external signals. Once the program 
receives a **SIGUSR1** signal, it should save one image into 
**/dev/shm/stillimage.jpg**, where gpsphotologger will find it. 
The script will add georeference tags, and save finally the 
photo into permanent storage. The directory /dev/shm/ was 
choosen because on the Raspberry Pi default installation, it is 
shared memory (quick RAM instead of slow SD-card).

This command line will start raspistill with suitable options:

```
/usr/bin/raspistill --output /dev/shm/stillimage.jpg --signal --timeout 0
```

# Running gpsd and ntp
