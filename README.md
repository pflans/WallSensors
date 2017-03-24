# WallSensors

## Purpose

A sensor package that includes 4 attachments for DHT-22 temperature and humidity sensors. Currently being used at Sasaki to measure 4 different points within the wall cavity to determine the effectiveness of the wall materials and insulation. Battery powered to avoid the need for external power the unit wakes up every 60 minutes to measure temperature and humidity from all 4 sensors, logs the data to the SD card, then puts itself back into deep sleep mode (Wi-Fi off). Once a day at midnight the unit will turn on Wi-Fi and transmit all of that days data points to Ubidots. This once-a-day use of Wi-Fi takes the unit from a about a week of uptime (with Wi-Fi always on) to about a month of uptime with a 6000 mAh battery.

## Hardware
* DHT-22 temperature and humidity sensor 
* Particle Photon microcontroller
* SparkFun Level Shifting microSD breakout
* microSD card 
* 1kΩ resistors
* SparkFun Photon Battery Shield (if fuel gauging is required)
* LiPo battery

## Dependencies 
* ADAFRUIT_DHT (0.0.2)    
* SPARKFUNMAX17043 (1.1.2)    
* HTTPCLIENT (0.0.5)    
* SDFAT (0.0.3)    


![Breadboard View](/imgs/sensorpack_bb.png?raw=true "Breadboard View")

![PCB View](/imgs/sensorpack_pcb.png?raw=true "PCB View")
