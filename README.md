# raspberry-pi-gpsphotologger
Python script to take georeferenced photos on a Raspberry Pi, using raspistill and gpsd.

This script acquires georeferenced photos on a Raspberry Pi. 
Photos are taken by an instance of the raspistill program, which 
must be running with the "--signal" parameter. GPS position is 
provided by a running gpsd instance.

System time must be syncronized with the GPS time, so that the 
exact position of the captured photo will be interpolated by 
reading subsequent positions coming from the GPS.
