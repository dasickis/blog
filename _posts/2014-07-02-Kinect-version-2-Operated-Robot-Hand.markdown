---
layout: post
title: Kinect v2 Operated Robot Hand
category: posts
---

<p align="center"><img src="/assets/robot/robot-hand-at-nui.png" /></p>

On June 21st, we attended a [NUI Central Kinect hackathon](http://kinectforwindowshackathon.challengepost.com/), where we created a hack project around the version 2 Kinect. Using this device, we worked with several other hackers to create a Kinect controlled robot hand, which won 2nd place at the hackathon.

We specifically coded the finger controlling server, which you can view on our [Github repository](https://github.com/Self-Driving-Vehicle/hand-controller)

### The Basics

The hack team we were part of centered our efforts on affecting a robot hand through the Kinect over a network. Thus, our hack project consists of three components: a server that receives commands and actuates the connected hand, a Kinect powered finger detector, and a Kinect powered object sensor.

<p align="center"><img src="/assets/robot/robot-trial-at-nui.png" /></p>

### The Kinect

Created for use with the Xbox One, the new Kinect offers an upgrade in perception quality. We adapted a finger detection algorithm so that Kinect can calculate the position of each finger. (link to who). From these positions, a finger “curl” degree can be worked out, and be passed to the hand controlling server.

For object detection, we applied several algorithms to remove the background and reveal objects that enter the Kinect’s field of view. Then, we can calculate the orientation and position of each object. For the hack, we used a subset of this algorithm to have the hand wave to any object that gets within a certain distance.

<p align="center"><img src="/assets/robot/robot-kinect-at-nui.png" /></p>

For real time communication, we used a socket.io client called [socketio4net](http://socketio4net.codeplex.com/). We also built an HTTP post system as a backup communication platform.

```csharp
// Be sure to have installed/imported the socketio4net library
Client socket;

// Inside the initialization function:
    socket = new Client("http://127.0.0.1/"); // change to socket.io server
    socket.Opened += SocketOpened;

// When a message should be sent to the server
    socket.Emit("command", "manual-{0}-{1}-{2}-{3}-{4}", th, in, mi, ri, pi);
```

### The Hand

We wanted the robot controlling to be over the network, so we used the rc-car-controller node server code as a base. A Raspberry pi ran this server, connected to an Arduino Uno. The Arduino controls five servo motors (one for each finger), which pull strings attached to the robot hand’s fingers.

<p align="center"><img src="/assets/robot/robot-connect-at-nui.png" /></p>
Node.js - Raspberry Pi - Arduino UNO - Servo Motors - Hand

The server receives socket.io or HTTP post calls to determine what position to set each finger to. The Kinect programs can broadcast commands to the node.js server at an overwhelming pace, so rate limiters were used to mitigate information floods. In **hands.js**:

```javascript
  // example servo setting - for use with Johnny-five
  var thumbServo = {
    pin: 9,
    range: [0, 180],
    type: "standard",
    startAt: 0,
    center: true,
  };

  // insert the servo into the Arduino board
  board = new five.Board();
  board.on("ready", function() {
    arduinoServos = {
      thumb: new five.Servo(thumbServo),
    }
    board.repl.inject({
      s: arduinoServos
    });
  }

  // rate limiter
  curr_time = (new Date).getTime();
  // basically, one set, ignores all commands until after X milliseconds
  if (curr_time - last_time < 5000) {
    last_time = curr_time;
    return; 
  }

  // Update the finger servos
  function fingerChange (finger, value) {
    arduinoServos[finger].to(value);
    
    board.repl.inject({
      s: arduinoServos
    });
  }
```

With this the Kinect can interpret the finger positions of a person, then send those values over the network to a server to manipulate the robot hand.


