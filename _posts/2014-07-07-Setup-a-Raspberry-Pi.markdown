---
layout: post
title: Setup a Raspberry Pi for General Development
category: posts
---

The Raspberry Pi is useful for teaching about computers. However, the bare bones nature of the device leads to an extensive setup requirement.

First, Raspberry Pi does provides a guide for getting it up and running

* http://www.raspberrypi.org/help/quick-start-guide/
* http://www.raspberrypi.org/documentation/

So why this article? This is my own personal, comprehensive take on setting up a Raspberry Pi, with extra instructions involving pitfalls I've encountered along the way. I will go from having just the $35ish Raspberry Pi, to having a fully operational wireless node server.

Note: Much of this article is also applicable to other pocket sized single board computers.

### Peripheral Needs

A desktop needs to be connected to a lot of stuff to work, and the Raspberry Pi is no exception. Here’s a list of things you’ll need, and what they’re used for:

* SD Card (> 4MB)
 * Store data and the operating system
* Micro USB cable (possibly with A/C adapter)
 * Power the RPi
* Monitor with HDMI cable
 * To see the what’s going on
 * The RCA video port is an alternative video port that can be used
* USB keyboard (maybe with USB mouse)
 * For computer control
* USB Wireless Adapter
 * For wireless (note that removing it from the RPi while on may cause a reboot)
 * Or an Ethernet cable for wired internet

The USB hub can be a valued friend, as there are only so many ports available. Also keep in mind the Raspberry Pi outputs a wimpy amount of electricity through the USB. Hungry devices like a USB Camera need to be run through a powered USB Hub.

You can connect the micro USB to an A/C adapter, another computer, or even a powered USB hub.

<p align="center"><img src="/assets/raspberry-pi-connected.png" /></p>

### Installing the Software

Assuming you haven’t gotten a pre-installed SD card, you’ll need to install an operating system on the SD card to use the Raspberry Pi. The company website provides a nice selection to choose from (http://www.raspberrypi.org/downloads/). In my case, I used Raspbian, the bog standard default. The device’s components aren’t that strong, so certain operating systems can’t run on it.

A warning: using NOOBs to install your operating system can insert presets you may not want such as forced HDMI, preventing use the RCA video output. If you’ve already done so and it’s already “helpfully” put in unwanted settings, you can modify it through the config.txt file (http://www.raspberrypi.org/documentation/configuration/config-txt.md). The added settings are generally added at the bottom of the file.

Once you’ve downloaded the operating system to a computer with an SD card port, the next step is to insert the SD card and format it. The SD Association (http://www.sdcard.org/) provides a SD Formatter for Window and Apple computers. Once you’ve formatted the card, you can drag the downloaded operating system into the SD card.

Note: I’ve encountered a problem with the formatter not properly freeing all the available memory. Be sure the formatter is set to do a full format, and I’ve had to use a previous version of NOOBs for it to access all the memory on the card.

After the files have been copied to the card, you can transfer the card to the Raspberry Pi and run it. The default account/password should be pi/raspberry.

### Getting the Wireless Ready

You can skip this step if you plan on using the Ethernet cable instead of a USB wireless adapter.

The newer versions of Raspberry Pi should be able to automatically detect your wireless device. Otherwise, you may have to Google your wifi model + Raspberry Pi. Regardless, you’ll need to set up the wireless configuration. The Raspberry Pi website now has documention on this step (http://www.raspberrypi.org/documentation/configuration/wireless/README.md). 

First, make sure the network interface file is correctly configured:

* sudo nano /etc/network/interfaces

```
auto lo
iface lo inet loopback

iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf

iface default inet dhcp
```

The network details can then be added to the wpa_supplicant.conf:

* sudo nano etc/wpa_supplicant/wpa_supplicant.conf

```
network={
 ssid="hotspotname"
 psk="mypassword"
}
```

An alternative solution is to run the built-in Desktop application “startx” and use the built in Wifi config. Though, the network interface file may not be setup properly, and you’ll have to prepare that manually.

On a raspberry pi, the wireless USB dongle will utilize power saving, which can result in a dropped connection, so a configuration file should be created to turn that feature off.
[Tutorial site](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/test-and-configure#fixing-wifi-dropout-issues)

* Create the config file:
 * sudo nano /etc/modprobe.d/8192cu.conf
* Paste in the file:
 * # Disable power saving
 * options 8192cu rtw_power_mgnt=0 rtw_enusbss=1 rtw_ips_mode=1
* Some other guides state to set rtw_enusbss=0, use whatever works.

Note: you may also want to update the Raspberry Pi firmware: there exists an easy tool to do so (https://github.com/Hexxeh/rpi-update)

* sudo apt-get install rpi-update
* sudo rpi-update

Extra Credit:
It can be a hassle adding network details for each new location, so some have setup the Raspberry Pi as an ad-hoc wireless hotspot instead. Possibly more details from me in a future post, but for now, here’s a link to a [tutorial](http://www.novitiate.co.uk/?p=183).

### Install Node.js

The Raspberry Pi was built to teach about python and computers, so it already has numerous programs and resources available for it. Because I like making things harder for myself, I’ll be using Javascript Node.js instead. There are several potential ways to install node.js on a Raspberry Pi, and as the device gains popularity, and simple command may eventually be provided to complete this step. As Raspbian is an offshoot of Debian, installation instructions for that OS are generally applicable.

First, all the existing programs should be updated on the Raspberry Pi

* sudo apt-get upgrade
* sudo apt-get update

Then, I download the latest node.js as a Debian package and install it

* sudo wget http://node-arm.herokuapp.com/node_latest_armhf.deb
* sudo dpkg -i node_latest_armhf.deb

To test this, I create a node.js script (server.js) and run it:

```javascript
var http = require('http');
http.createServer(function (req, res) {
 res.writeHead(200, {'Content-Type': 'text/plain'});
 res.end('It's a trap!');
}).listen(80, '127.0.0.1');
console.log('Server running at http://127.0.0.1:80/');
```

* sudo node server.js
 * node.js normally doesn’t like using low ports, so I cheat with sudo
 * Ideally you use a higher port number, like 1337 or 8000

Bonus: if you want to use the Raspberry Pi’s GPIO pins, consider disabling the blacklists for them in the /etc/modules files.

* sudo nano /etc/modules
 * #blacklist spi-bcm2708


##### [Comments in Discourse](http://www.sherecar.org/t/controlling-a-rc-car-with-node-js/113)
