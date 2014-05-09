---
layout: post
title: Derivatived Object Tracking RC Car Hack Part 1
category: posts
---
{{page.title}}

These next two posts, I'll be describing an RC car hack we made to get it to track objects with a camera and actuate towards them. Part 1 will be about the hardware modifications we made, and part 2 will be the software we developed to run it.
<p align="center"><img src="/assets/RC-car-picture.jpg" /></p>

### Components

* RC Car - 1/10 Circuit Stadium Truck
 * 7.2 Volt battery
 * Steering servo motor
 * Throttle servo motor
* Arduino UNO
 * Serial/USB cable connecting this to the Raspberry Pi
* Raspberry Pi
 * Wifi USB dongle
 * Raspberry Pi Camera

### Power

<p align="center"><img src="/assets/RC-car-wiring.jpg" /></p>

The 7.2 RC car battery is used to power all the components. As an alternative, the micro USB port on the Raspberry Pi is used to conserve battery life during testing.

For our RC car model, the battery directly feeds into the throttle servo motor, so we connect all the devices' power pins to it (steering motor and Arduino UNO). A breadboard was used for this, though soldering them together would have sufficed.

Technically, the Arduino should have been fed a 5V source, but there are mechanisms within the device to account for the difference. Thus, we did not need to use a voltage regulator to adjust the voltage down from 7.2V. The Raspberry Pi does not have as much regulation, so we used the "USB to serial" connection to provide power to the Raspberry Pi.

Normally, this wire is used to provide power from computer to Arduino UNO, so we used this property to power everything via computer for testing: (Computer → Raspberry Pi -> Arduino UNO -> Steering)

As a side note: the throttle motor requires more power than the USB port provides, so it doesn't run during testing.

### Combining

First, we removed the plastic cover to the RC Car, then removed the servo wires connectors from the Radio controller. Power wires (red) are described in the previous section.  Throttle and Steering servo control wires are connected to the 9/10 PINs on the Arduino UNO for easy servo control.

We also 3d printed some generic mounts for the Raspberry Pi and Arduino UNO.

* Raspberry Pi mount: http://www.thingiverse.com/thing:43587
* Raspberry Pi camera holder: http://www.thingiverse.com/thing:92208
* Raspberry Pi camera mount: http://www.thingiverse.com/thing:139873

The Raspberry Pi and Arduino UNO are currently taped onto the chassis of the RC Car. The camera mount is taped on the front of the car, though some consideration is being made to shift it further back.

##### [Comments in Discourse](http://www.sherecar.org/t/blog-post-opencv-with-an-rc-car/115)
