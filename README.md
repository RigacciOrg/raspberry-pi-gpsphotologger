# raspberry-pi-gpsphotologger
Python script to take georeferenced photos on a Raspberry Pi, using raspistill and gpsd.

This script acquires georeferenced photos on a Raspberry Pi. 
Photos are taken by an instance of the **raspistill** program, 
which must be running with the "--signal" parameter. GPS 
position is provided by a running **gpsd** instance.

System time must be syncronized with the GPS time, so that the 
exact position of the captured photo will be interpolated by 
reading subsequent positions coming from the GPS.

# How to launch a raspistill instance

The gpsphotologger script must find a running instance of the 
**raspistill** program (provided by the Raspbian 
**libraspberrypi-bin** package), otherwise it cannot take a 
photo from the Raspberry Pi camera. This choiche was made 
because we wish to abstract all the issues concerning camera 
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

To abstract all the issues of reading GPS data position, we used 
the **gpsd** daemon. Many GPS devices provides position data 
with coarse granularity (e.g. one position every one second). 
This is not optimal, just think that moving at 100 km/h, you 
move about 30 meters in one second!

So the gpsphotologger will take two GPS reads: one before the 
photo shoot and one after. The position will be linearly 
interpolated in between. For this to occur nicely, system time 
should be properly synced with the very accurate GPS time.

There are many readings about syncing system time with **gpsd** 
and **ntpd** on the Raspberry Pi. Here you can find
[some notes](https://www.rigacci.org/wiki/doku.php/doc/appunti/hardware/raspberry_explorer#time_syncronization_with_gps_and_ntp).
