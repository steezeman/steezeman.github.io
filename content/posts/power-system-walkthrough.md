+++
title = 'Power System Walkthrough'
date = 2024-08-04T22:00:00-06:00
featured_image = 'images/cab-electrical-system-aug-24.jpeg'
draft = false
summary = 'Technical details and evolution of my electrical system'
+++

_I've cobbled together this electrical system over the last year and a half (May 2023 - August 2024). It's seen a lot of changes and upgrades, and there's always more to do. This post is my attempt at explaining some of the choices I made along the way, and I hope it helps others understand some of the pitfalls of this space._


### From Humble Beginnings...

I've owned my Tacoma since October 2022. As soon as I installed a Go Fast Camper in May 2023, the electrical system has been the single most time consuming and expensive part of my build. I love working on it. 

This is a very different type of electrical engineering than what I'm used to - knowledge in schematic capture and PCB layout doesn't translate to higher-level integration as well as I had hoped. My first few attempts weren't great, but with time I learned how to build a good system that feels robust and safe.

The system started with one Renogy 12V 100Ah LiFePo4 battery. Then it grew to two. Soon, I had 300W, then 400W of solar panels on the roof, and was charging 30A @ 12V from the alternator, using a cheap charge controller from Renogy.

Soon I added Starlink and a fridge, and the game changed. I was spending more time in the truck, and the two batteries and the charging I had wasn't enough. I also recognized that I was outgrowing the Renogy system that I had set up. Renogy seems nice on paper and has nice price tags, but its products are of poor quality, not built to last, and have limited interfacing options with automation systems.

### Enter Victron

Victron is a European company that makes high-quality products for enegry storage systems. I liked the specs of their products, but wasn't totally sold on their system until I learned about their open protocols - all of their [Ve.Can](https://www.victronenergy.com/upload/documents/VE.Can-registers-public.pdf) (CAN), [Ve.Direct](https://www.victronenergy.com/upload/documents/VE.Direct-Protocol-3.33.pdf) (Serial), and [Bluetooth](https://community.victronenergy.com/questions/187303/victron-bluetooth-advertising-protocol.html) protocols are open, and they have great integrations with automation systems like Node.RED. I was sold.

I also stumbled on Battle Born Batteries, which at the time seemed like the best battery for my application. They make a nice battery, assembled in the US, and offered a self-heated option for cold environments (spoiler alert, I would not use Battle Born again).

Once again, the system grew. I upgraded my alternator to a high-output version ([more in this forum post](https://www.tacomaworld.com/threads/alternator-swap-180a-highlander-and-270a-dcpe.817559/)). Eventually I added two more chargers to pull 120A @ 12V from the alternator. I learned a lot of tricks along the way, like tricking the Engine ECU into thinking that the A/C compressor was running when I needed more power, resulting in higher RPMs and more current capability. 

I started with two Battle Born batteries for a total capacity of 200Ah at 12V, and as winter set in, I once again doubled the pack size, choosing to use a 2S2P architecture, and arranged my four battery chargers (two of which have isolated outputs) as 2S2P so I could re-use my existing non-isolated Orion-Tr 12V chargers. One nice benefit of this setup is that when the alternator charges the pack to 100%, it guarantees that the pack is balanced without an external balancer.

I wired everything up, and before I had a chance to do any cleanup or wire management, I was on my way to Alaska, in December! Here's a picture of the electrical system on that trip. I'll be the first to admit that I'm lucky that there were no major incidents with this system. I have learned a lot about proper fusing and wire management since then.

![my janky electrical setup in alaska](/img/bed-electrical-alaska.jpeg)

### Everyone else is using 12V, why are you using 24V?

In hindsight, 12V would have been a better choice. 24V is nice in theory because it lets you use smaller wires, and generating 48V with a DC-DC converter so you can run a PoE injector for Starlink gets a little easier, but this is a small truck with small loads, and the highest current wires are 12V from the alternator (~120A) already, so the effect is almost zero. 

I'm considering a switch to 12V soon, before I install an inverter-charger.

![my current setup](/img/cab-electrical-system-aug-24.jpeg)

### Component list

- (2x) Victron Orion Smart 12/12-30 battery charger - non-isolated (charges lower 12V part of the pack)
- (2x) Victron Orion Smart 12/12-30 battery charger - isolated (charges upper 12V part of the pack)
- (1x) Victron SmartSolar MPPT 150-35 solar charge controller (adjustable output from 12V to 48V, so this charges across the entire pack)
- (1x) Victron Phoenix 24V 1200VA inverter
- (4x) Battle Born Batteries - 12V 100Ah each - arranged 2S2P
- (1x) DC Power Engineering [270A alternator XP High Output Alternator](https://www.dcpowerinc.com/products/270-amp-xp-high-output-alternator-toyota-tacoma-3-5-2gr-fks-v6) with a smaller diameter pulley
- Custom laser cut bus bars (topic for another post?)
- Polar Wire Arctic Flex (I used their power lugs and kept wire sizes to 1/0 AWG and 6 AWG for simplicity)
- Lots of fuses

### My Ideal System

Knowing what I know now, this is what I would build:

- (4x) [12V Pylontech batteries](https://icmontana.com/collections/pylontech-batteries/products/pylontech-rt12100g31) - arranged in 1S4P. These are far better than the Battle Born batteries they compete with and interface natively with the Victron GX hubs via Ve.Can. No external shunt required.
- (3x) [Victron Orion XS 12/12-50](https://icmontana.com/collections/dc-dc-converters/products/orion-xs-12-12-50a-dc-dc-battery-charger) - higher current, more efficient, and they have a VE.Direct port to allow interface with Victron GX hubs. 
- (1x) [Victron SmartSolar MPPT 150-35](https://icmontana.com/collections/solar-charge-controllers/products/smartsolar-mppt-150-35-150-45) solar charge controller
- (1x) [Victron MultiPlus-II 12/3000/120-50](https://icmontana.com/collections/inverter-chargers/products/multiplus-ii) 3000W inverter/charger
- (1x) DC Power Engineering [270A alternator XP High Output Alternator](https://www.dcpowerinc.com/products/270-amp-xp-high-output-alternator-toyota-tacoma-3-5-2gr-fks-v6) with a smaller diameter pulley

I've considered adding more alternators to the truck, but the truth is that the engine as-is isn't very powerful at low RPMs. I think 150A @ 12V is the most stress I'd ever want to put on the engine. Maybe I'll add a way to switch some of the chargers on and off for a dynamic braking effect whilst driving around, or for a power boost when I need more torque.

### Future topics

I'd like to dedicate a few future articles to:

- Solar setup (panel mounting, managing heat on the panels, etc.)
- Alternator swap specifics (how I got the RPMs high enough with mechanical, electrical, and ECU software mods to make this work)
- Power distribution considerations
