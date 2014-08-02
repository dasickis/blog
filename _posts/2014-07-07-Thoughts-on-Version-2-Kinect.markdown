---
layout: post
title: Thoughts on Version 2 Kinect
category: posts
---

On July 15, 2014, Microsoft released the next generation “Kinect for Windows v2” SDK, found in the Xbox One. The SDK is still under development, but the new device iteration boasts enhance features and promises greater applications. I recently got my hands on one from a [Hackathon](http://kinectforwindowshackathon.challengepost.com/) at [Grind Spaces](http://grindspaces.com/), and wanted to share my notes on them. This will be simple blog post exploring the sensor properties and some of the options available to people interested in using the device.

Be sure to explore the [Microsoft website](http://www.microsoft.com/en-us/kinectforwindows/%EF%BB%BF) for further details, to order your own device, or download the SDK.

<p align="center"><img src="/assets/kinect/kinectv2.png" /></p>

## Overview of the Device and SDK

The version 2 Kinect is the same one as the one used in the XBox One (minus the logo). The camera itself provides three types of data:

* 1080p color
* Infrared
* Depth sensing

The resolution and depth sensing range/clarity has been upgraded compared to the original Kinect. For example, test applications have been made that can detect minute changes in the skin to measure a person's heartbeat. The built in microphone is also sensitive, and can calculate the rough angle a sound originates from.

Keep in mind that the 2.0 SDK is **only** for the new Kinect. The original Kinect uses the 1.8 SDK instead. Some libraries may require the 1.8 libraries, even if the new Kinect is the device being used. Also, the new version requires Windows 8 and a USB 3.0 connection.

## Body Sensing

The Kinect is particularly designed to see bodies, and version two can track six people, with 25 "joints" for each person. This enables the creation of a skeleton for a tracked person. Version 2 can track more joints than the original, but there exists third party libraries that allow an even greater degree of precision. For example, you can see how we used the Kinect v2 to shadow hand movements in one our previous [blog posts](http://blog.derivatived.com/posts/Kinect-version-2-Operated-Robot-Hand/).

To detect bodies, the Kinect looks for faces, then shoulders if the face is obscured. With a known reference point, the rest of the body can be more easily captured. When programming  the SDK automatically organizes the this information in *BodyFrame* of *BodyFrameReader*. It also has a built in function to tell if the hand is open, close, or pointing.

<p align="center"><img src="/assets/kinect/kinectv2-from-blog.jpg" /></p>

## Depth Sensing

An important aspect of the Kinect functionality is the ability to tell how far away everything is. Singular cameras lack the ability to see in 3-D, while stereoscopic cameras can require a lot of processing power/algorithms to work. Instead, Kinects actually emits infrared beams to create more information and generate a 3-D map.

The original emitted a grid pattern in infrared and measured how far apart the dots were. This technology belonged to PrimeSense, but the company was bought by Apple. As a result, the version 2 Kinect switched to using time of flight. Here, the devices measures how long it takes a beam of light to reach each location and calculates the distance. This does have it's own set of problems, notably generating ghost shadows where the light point never returned.

As a side note, **Kinect Fusion** is a powerful library that utilizes the depth sensor to build a 3-D model of what it sees. The Kinect for Windows SDK v2.0 has not integrated this feature at the point of this post, so using the 1.8 at the same time may be necessary.

<p align="center"><img src="/assets/kinect/kinectv2-ir-image-from-wikis.png" /></p>

## In Robotics

The generous suite of sensors on the Kinect has made the device attractive in the use of robotics. Unfortunately, both version of the Kinect require external power, and the adapter ends in an electrical socket plug. Alternative current batteries are generally too expensive to be feasible, so a common solution is to snip off the wire leading into the AC adapter (you should be able to see electric information on the brick), or at least with a cheap third party version. The exposed wires would then be connected to a 12 volt power source. The version 2 Kinect is still new, so third party knockoff adapters still don't exist yet.

For people that do not wish to run Windows on their robot, there exists a library called [OpenKinect](http://openkinect.org/wiki/Main_Page) that allows other operating systems, such as Linux, to interface with the Kinect. There is not version for 2.0 yet, but it should only be a matter of time before that gets developed.

## More Functionality

The SDK also provides additional tools to help with development, such as a gesture builder program, which gives developers a way to introduce custom interaction. Also, multiple applications using the Kinect can be run at the same time. A useful new feature is the Kinect Studio, which can record and play back the sensor's data stream. This aids testing be providing a static input, and allows people for work on Kinect development without needing the device itself.

### Additional Resources

* [Documentation about SDK](http://msdn.microsoft.com/en-us/library/dn791988.aspx)
* [Public Preview July 15, 2014](http://msdn.microsoft.com/en-us/library/dn772637.aspx)

##### [Comments in Discourse](http://www.sherecar.org/)