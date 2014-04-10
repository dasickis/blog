---
layout: post
title: OpenCV Tutorial on Face Tracking - Raspberry PI Camera
category: posts
---
{{page.title}}

This tutorial covers the use of on open source computer vision library (OpenCV) on a Raspberry Pi Camera for object tracking. The result of this can be used to direct an RC Car towards the face.

### Physical Components - Same as the previous RC Car Post
* Raspberry Pi - the micro computer we used to operate everything
* Arduino UNO - for actuating the motors on the car
* Radio Controlled Car
* [Our Post on Controlling an RC Car](http://blog.derivatived.com/posts/Control-Your-Robot-With-Node.js-Raspberry-PI-and-Arduino/)

### Third Party Libraries
* node.js, with npm (Node Packaged Modules)
 * wget http://node-arm.herokuapp.com/node_latest_armhf.deb
 * sudo dpkg -i node_latest_armhf.deb
* Node modules to control the RC Car
 * npm install johnny-five
* Install "StandardFirmata" on the Arduino
 * In the Arduino IDE: File > Examples > Firmata > StandardFirmata
* Install Python libraries - OpenCV and PiCamera
 * sudo apt-get install libopencv-dev python-opencv python-dev python-picamera
 * sudo pip install numpy

### PiCamera
To simplify things, we're going to use a python library for the Raspberry Pi Camera, called [picamera](http://picamera.readthedocs.org/). The Recipies provided by the documentation are a useful reference. For now, we'll just have the camera take a picture and covert it into an OpenCV friendly format.

```python
import io
import picamera
import cv2

#saving the picture to an in-program stream rather than a file
stream = io.BytesIO()

#to speed things up, lower the resolution of the camera
CAMERA_WIDTH = 320
CAMERA_HEIGHT = 240

with picamera.PiCamera() as camera:
    camera.resolution = (CAMERA_WIDTH, CAMERA_HEIGHT)
    #capture into stream
    camera.capture(stream, format='jpeg')
#convert image into numpy array
data = np.fromstring(stream.getvalue(), dtype=np.uint8)
#turn the array into a cv2 image
image = cv2.imdecode(data, 1)
```

### OpenCV
[OpenCV](http://opencv.org/) is an open source computer vision library that can be used for many vision applications. Here we're going to use python to have cv apply a face recognition to the captured image.

To recognize faces using OpenCV, we're going to use a method called "Cascading Classifiers". Simply put, the algorithm "cascades" through a series of rectanges around a given image, and checks to see if there is a match with the "classifier".

OpenCV provides from cascade files in their [source code](https://github.com/Itseez/opencv/tree/master/data). Two common cascade files are "Haar" and "LBP". Unlike LBP, Haar files use decimal numbers, so it is slower but more accurate.

```python
// After: image = cv2.imdecode(data, 1)

//load a cascade file for detecting faces
face_cascade = cv2.CascadeClassifier('lbpcascade_frontalface.xml')

//convert to grayscale, which is easier
gray = cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)

//look for faces over the given image using the loaded cascade file
faces = face_cascade.detectMultiScale(gray, 1.3, 5)
for (x,y,w,h) in faces:
    //opencv has built in image manipulation functions
    cv2.rectangle(image,(x,y),(x+w,y+h),(255,0,0),2)

//use opencv built in window to show the image
// leave out if your Raspberry Pi isn't set up to display windows
cv2.imshow('image',image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Adding Commands
Once we have the location of a face in an image, we can pass servo commands to node.js/johnny-five based on its location in relation to the image. Turn left if the face is left, and so on.

```python
#AFTER: cv2.rectangle(image,(x,y),(x+w,y+h),(255,0,0),2)

    #math to find the center of the rectangle and how far off center if is
    offCenter = x + w/2 - CAMERA_WIDTH/2

    if offCenter < 10:
        command = 'turn-left'
    elif offCenter > 10:
        command = 'turn-right'
    else:
        command = 'turn-neutral'
    #make a http GET call to node server
    #NOTE: set 'server_address' to your actual address
    server_address = '127.0.0.1'
    urllib2.urlopen(server_address+'/command/?command='+command)
```

For the node server to process the command, we'll just keep it simple with GET calls translating to johnny-five Arduino commands.
```javascript
// Initialize express and server
var express = require('express')
var app = express()
  , server = require('http').createServer(app);

// Access server through port 80
server.listen(80);

// Set index.html as the base file
app.get('/', function (req, res) {
});

// allow commands to be send via http call
app.get('/command/', function (req, res) {
  //get the 'command' input and send the Arduino command
  var command = req.query.command;
  if (command == 'turn-left') {
    arduinoServos.steering.to(40);

    board.repl.inject({
      s: arduinoServos
    });
  }
  else if (command == 'turn-right') {
    arduinoServos.steering.to(100);

    board.repl.inject({
      s: arduinoServos
    });
  }
  else {
    arduinoServos.steering.to(75);

    board.repl.inject({
      s: arduinoServos
    });
  }
});

```

### Additional Information
In our rc-car-controller, we've split up the picamera and opencv python scripts into separate files, and have the image be sent over via socket. This allows the opencv.py to be run on a faster computer.

The Github repository for our [RC Car Controller](https://github.com/Self-Driving-Vehicle/rc-car-controller)

Some OpenCV-Python [Tutorials](http://docs.opencv.org/trunk/doc/py_tutorials/py_tutorials.html)

##### [Comments in Discourse](http://www.sherecar.org/)