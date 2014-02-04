---
layout: post
title: Servo Motor Wiring for People Who Never Tried It Before
category: posts
---
{{page.title}}

This is a basic summary about how we connected an Arduino Uno to an RC car's servo motors.

It's written from the viewpoint of someone unfamiliar with electrical work, so beginners might find this useful. <i>Note of caution</i>: electricity is dangerous, not just to people, but devices as well. "Undo" doesn't work on burned devices. Sensitive equipment can also be destroyed by static electricity, so it's a good idea to "ground" yourself beforehand to disperse any built up charge.

The final result is to use an Arduino Uno to run our RC car's motors rather than the radio unit.
<p align="center">[RC Car with Arduino UNO]({{ site.url }}/assets/RC-car-wired.jpg)</p>

### RC Car Parts

Looking at our RC car, the throttle and steering motors are connected together by a radio receiver. We'll be disconnecting the reciever and linking the motors to the Arduino Uno instead.

Servo motors have three connections: power, ground, and signal. There is <b>no</b> guarenteed color scheme for what each wire pertains to, so it's a good idea to double check beforehand.
Usually though, the colors are:
 * Dark (black/brown) for ground
 * Red for power
 * Other (white/orange) for signal

When working with electrical wiring, remember that power (+) flows to ground (-). For basic wiring, always make sure power and ground are grouped separately. The only connection between the two should be the devices you wish to run.
 * Mixing multiple power sources is for advanced users, since electrical inequalities between them could cause damage.

Keep the electrical formulas in mind, as there may be a need to add resistors for more sensitive components to reduce the amount of power sent. For example, LED lights generally require an additional resistor in serial or it will burn out.
 * Ohms Law: V (volts) = I (Current amperes) * R (resistance Ohms)
 * Power Equation: Power (watts) = I (Current amperes) * V (Volts)

Servo motors, such as the ones in the RC car, have a Pulse-Width Modulation signal connection that controls the position/speed of the motor. Normally, this signal consists of pulses, but the Arduino can send them automatically, so the wire can be directly connected to the appropriate Arduino pin.

### Controlling Just the Throttle

To have the Arduino run the throttle motor, direct connections can be made between the two. Ground (brown) should link to the "ground" pin. Signal (orange) should link to a PWM pin, such as pin #9. You can power the Arduino via USB and ignore connecting power; otherwise, power (red) should link to the 5V pin.

To setup the Arduino to control the motor, start up the Arduino program on a connected computer (Download the program from [online](http://arduino.cc/en/Main/Software)), then open the Servo example file. If needed, modify the Servo number to the correct one. <i>(Look to official Arduino tutorials for more details)</i>. When ready, click the upload button to push it to the connected Arduino. The throttle motor should automatically start running.

### Controlling Both Throttle and Steering

In our RC car, only the throttle motor is connected to the battery, so we'll need to connect power to both servos. For simplicity, we'll use a breadboard to connect everything. In a breadboard, the center lines are linked horizontally, and the outer lines are linked vertically.
<p align="center"><img src="/images/_posts/breadboard-labeled.jpg" /></p>

Connect the ground wires together to the same breadboard group, and power wires to a separate breadboard group.

In the Arduino program, add a second Servo motor variable and connect the steering motor's PWM signal to the new Arduino pin.

Our end result looked like:
<p align="center"><img src="/images/_posts/RC-car-wired-labeled.jpg" /></p>

[Comments on Discourse](http://discuss.derivatived.com)

### Links

* [Discourse Discussion Site](http://discuss.derivatived.com)
* [GitHub Code Repository](https://github.com/Self-Driving-Vehicle)
* [Meetup Webpage](http://www.meetup.com/Self-Driving-Vehicle/)
