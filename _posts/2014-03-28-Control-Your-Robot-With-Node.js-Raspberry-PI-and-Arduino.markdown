---
layout: post
title: Control Your Robot With Node.js - Raspberry PI and Arduino
category: posts
---
{{page.title}}

Advancements in Javascript, node.js, have simplified communication between robot and user. Using two choice nodejs libraries, socket.io and johnny-five, you can quickly put together a system to control a robot using a web browser.

### Physical Components We Used
* Raspberry Pi - the micro computer to operate everything
 * Though we used a Raspberry Pi to host the node server, any computer with node.js installed will suffice
* Arduino UNO - for actuating the motors on the car
* Radio Controlled Car
 * We disconnected the on-board radio and replaced the wires with ones connecting to the Arduino. See our previous post on wiring a RC car for more information.
 * [Our Post on Servo Motor Wiring](http://blog.derivatived.com/posts/Servo-Motor-Wiring-For-People-Who-Never-Tried-It-Before/)

Warning: the Raspberry Pi does not have much power regulation. Improper power connections can burn it out.

### Third Party Libraries
* node.js, with npm (Node Packaged Modules)
 * wget http://node-arm.herokuapp.com/node_latest_armhf.deb
 * sudo dpkg -i node_latest_armhf.deb
* Install node modules:
 * npm install socket.io express johnny-five
 * socket.io - communication between browser and node server
 * johnny-five - Arduino control
 * express - web page formatting
* Install "StandardFirmata" on the Arduino
 * In the Arduino IDE: File > Examples > Firmata > StandardFirmata

### Node Server

To start with, we need to set up a [Node.js](http://nodejs.org/) server to facilitate communication between the web browser and Arduino. We're also going to use [Express.js](http://expressjs.com/) as a supporting web framework.

First step is to create a new **server.js** file and type in preliminary code for a node server.

```javascript
// Initialize express and server
var express = require('express')
var app = express()
  , server = require('http').createServer(app);

// Access server through port 80
server.listen(80);

// Set '/public' as the static folder. Any files there will be directly sent to the viewer
app.use(express.static(__dirname + '/public'));

// Set index.html as the base file
app.get('/', function (req, res) {
  res.sendfile(__dirname + '/index.html');
});
```

Then, create a **index.html** in the same folder and put in some basic HTML code. Start up the node server with **node server.js** and visit the address using a web browser. You should see the contents of **index.html**.

> In some cases, only administrators can run code that uses low ports (ie: 80). So **sudo node server.js** might be necessary instead.

##### Socket.io

[Socket.io](http://socket.io/) is a way to provide real time cross-browser communication. We'll use this to forward user actions in the browser to the Arduino. In the same **server.js**, add code for some basic socket.io functionality:

```javascript
// Link socket.io to the previously created server
var io = require('socket.io').listen(server);

// When someone has connected to me...
io.sockets.on('connection', function (socket) {
  // Send out a message (only to the one who connected)
  socket.emit('robot connected', { data: 'Connected' });
  
  // When I've received 'robot command' message from this connection...
  socket.on('robot command', function (data) {
    console.log(data);
  });
});
```

Now, when **server.js** is running, someone can use a socket.io client to connect and send data in real-time.

### Web Browser

**index.html** needs to be set up to act as a socket.io client so a web browser can be used to send commands to the node server.

```html
<!--Add two buttons a user can click-->
<button type="button" id="turn-left">Turn Left</button>
<button type="button" id="turn-right">Turn Right</button>

<!--Using JQuery for button events-->
<script src="js/jquery.min.js"></script>
<!--Socket.io library-->
<script src="/socket.io/socket.io.js"></script>
<script>
  $(document).ready(function() {
    // Connect to the node.js server. Change the IP address to the actual node server location.
    var socket = io.connect('http://localhost');
    // When I've received 'robot connected' message from the socket.io server...
    socket.on('robot connected', function (data) {
      console.log(data);
      // Send out a message to the server
      socket.emit('robot command', { command: 'nothing' });
    });
    
    // When the html buttons are clicked...
    $('#turn-left').click(function() {
      socket.emit('robot command', { command: turn-left });
    });
    $('#turn-right').click(function() {
      socket.emit('robot command', { command: turn-right });
    });
  });
</script>
```

Socket.io automatically generates the Javascript file required by the web browser, so you don't need to place this in the public folder. In our project on github, we use [Bootstrap](http://getbootstrap.com/) and [JQuery](http://jquery.com/) to boost the functionality of our web page. The Javascript libraries are placed in a **public -> js** folder.

### Johnny-five

[Johnny-five](https://github.com/rwaldron/johnny-five) is an Arduino framework for easier robot control. We'll use the library to translate received socket.io commands into Arduino commands. In **server.js**:

```javascript
var five = require("johnny-five")
    , board, servo;

// Global storage for Arduino Servos
var arduinoServos = {};

// Initialize connection to Arduino (will crash if none is attached)
board = new five.Board();

// When the connection is ready...
board.on("ready", function() {
  // Add a steering servo
  arduinoServos = {
    steering: new five.Servo({
      pin: 9,             // Send signal through Arduino Pin #9
      range: [0, 180],    // Servo value ranges: 0-180 (Check motors for actual range)
      type: "standard",   // Default "standard". "continuous" for cont. rotation servos
      startAt: 90,        // if you would like the servo to immediately move to degree
      center: false       // If true moves servo to center of range instead of starAt
    })
  };
  steering = arduinoServos.steering;

  // Inject the `servo` hardware into context; allows direct command line access
  board.repl.inject({
    s: arduinoServos
  });
});

//... In the section: socket.on('robot command', function (data)...
// Update Arduino motor values if the received command is a 'turn-left' or 'turn-right'
var command = data.command;
if (command == 'turn-left') {
  arduinoServos.steering.to(20);
  
  board.repl.inject({
    s: arduinoServos
  });
}
else if (command == 'turn-right') {
  arduinoServos.steering.to(160);
  
  board.repl.inject({
    s: arduinoServos
  });
}
//...
```

Remember to install "StandardFirmata" on the Arduino, so it can properly interpret Johnny-five from node.js.

### Running the Application
* Start up the nodejs server
 * sudo node server.js
* Open a web browser to the Raspberry Pi's internet location
* Control the RC Car

#### [Comments in Discourse](http://www.sherecar.org/t/blog-post-controlling-a-rc-car-with-node-js/113)
##### [The Github repository for our RC Car Controller](https://github.com/Self-Driving-Vehicle/rc-car-controller)