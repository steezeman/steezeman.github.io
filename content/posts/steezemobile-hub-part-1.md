+++
title = 'Steezemobile Hub Part 1: Exploring the Problem Space'
date = 2024-07-09T00:00:39-06:00
featured_image = 'images/steezemobile-hub-part-1.jpeg'
draft = false
summary = 'I want to automate the day-to-day functions of maintaining the system so I can spend less time "pulling levers" and analyzing the system and more time recreating outside.'
+++

### Context
I have been living in my Toyota Tacoma periodically for the last year, as work commitments allow me to get away. I've been all over the country, from Texas to Alaska. I have built a large energy storage system (4800Wh LiFePo4) that can be charged from either the engine (~1500W), solar panels (400W nameplate), or [in the future] from 120V AC.

### The problem

I want to automate the day-to-day functions of maintaining the system so I can spend less time "pulling levers" and analyzing the system and more time recreating outside.

This problem was most apparent in the harsh climate of Turnagain Pass in January 2024 - I spent a lot of time running the engine, watching solar generation during the 1-2 hours of decent sunlight in a day. That's prime time that I could have been skiing or working on "real" work. 

![steezemobile in alaska](/img/alaska-truck-january.jpeg)

The system needs control of the truck, all the sources/sinks in the system, and it needs to know the status of the batteries, the weather outside, and have a way to ask me what my preferences are for the next hours + days so it can minimize disruption and best use energy. As you can imagine, with all this info and control, there's some cool behaviors that the system could have to make it ultra-functional.


### Available solutions
Only a few companies offer overlading automation products - notably REDARC and Victron Energy have a few products, but most of them are glorified distribution panels. 

There's lots of home automation products out there, but none of them make sense for me. I don't have much space, I don't want to run more than one Raspberry Pi in the truck because I don't want to deal with the extra volume and power draw when one pi is plenty.

I've attempted to use a [Sequent Microsystems Home Automation HAT](https://sequentmicrosystems.com/) for my Raspberry Pi 4, but it isn't quite what I want for this build. I had it set up for a while with a python script to control/read the I/O, but I ran out of I/O and the ADCs weren't quite what I wanted.

I've ideated through and built a few iterations over the last 6 months for this hub - I'll spare the details for brevity.

### Plan

The automation system will replace the factory stereo and sit behind a tablet (iPad 10th Gen Cellular in my case)

The system will work with my vehicle, with a 12V starter and 24V house battery.

🧠 Raspberry Pi 4 as a system hub
- Communicates via I2C on GPIO header with my custom PCB
- PiHole DNS server
- Server for [web] app on the tablet or my phone

⏱️ Real-time controller board (custom PCB)
- STM32G4 (selected for medium cost and abundance of CANbus + analog peripherals)
- USB device connection to iPad (HID for steering wheel + Audio (mic and speakers))
- Audio DAC output and microphone input for improved cabin audio quality
- Raspberry pi header and mounting holes
- Inputs 
    - Digital signals from accessory and ignition lines (12V)
    - Steering wheel controls (audio, calls, etc)
- 24V high-side switched loads (from house battery)
    - Starlink supply
    - WiFi/cellular router
    - Battery heater control lines (4x)
    - Reverse charging 24V to 12V
    - Charger cooling fans (2x)
    - etc.
- 12V high-side switched loads (from starter battery)
    - Stereo amplifier
    - ARB air compressor enable + illumination line
    - Victron Orion DC/DC charger control (4x)
    - etc.
- Open drain outputs
    - LED strip control (x8)
    - Engine ECM A/C high idle control (via 120 ohm resistor)
- LoRa radio for long range communication
- Engine automation - I'll share the details for how this system will work in the next post.
    - RS232 aftermarket remote starter connection
    - CAN connection to OBD-II port (RPM, fuel level, etc)
    - LIN connection to climate control system (batteries are in the cab and can withstand to 140F, so active cooling may be needed in extreme climates. Batteries are self heating)
- VE.Direct and VE.Can ports for Victron devices in the system (shunt, inverter[/charger], MPPT)
- I2C Inertial Measurement Unit (IMU) for motion detection and leveling assist
- Temperature sensor inputs from alternator, cab, bed, outside

Stretch goals for later:
- Security system w/ Cameras and detection/classification of events
- LoRa + BLE dongle for remote monitoring of truck from phone when phone doesn't have cell connection (truck has Starlink on all the time so I can assume connection is good most of the time)


That's a lot of stuff! Most of it is extremely simple hardware. The audio stuff is new to me, so I'll have to learn how to do SAI and USB audio. Sounds like a fun challenge.

### Timeline

This is my full-time job at the moment as I look for my next "real" job - so I'm hoping to push out a schematic this week, and get the board layed out in the following week.

Much more to come!
