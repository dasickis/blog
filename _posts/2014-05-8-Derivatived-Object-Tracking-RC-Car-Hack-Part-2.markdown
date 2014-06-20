---
layout: post
title: Derivatived Object Tracking RC Car Hack Part 2
category: posts
---
{{page.title}}

Part 1: http://blog.derivatived.com/posts/Derivatived-Object-Tracking-RC-Car-Hack-Part-1/
Part 2 of our RC car hack is about the software we used to control it remotely and with a computer vision algorithm. The source code can be found on our Self Driving Vehicle github at: https://github.com/Self-Driving-Vehicle/rc-car-controller, so the code snippets will generally be rather minimal.

### Node Server (server.js)
First, we’ll start with the javascript side of things, which will control the Arduino Uno and provide a robot interface for users.

Node libraries we used (see <package.json>):

* johnny-five: easy to use library that simplifies communication between an Arduino and computer
* socket.io: communication protocol used between the web page and node server
* express.js: simple web framework to help manage web pages
 * body-parser: middleware for express.js

You can install these libraries automatically with node.js package manager in the package.json folder (npm install). Two major segments to consider are johnny-five for Arduino control and socket.io communication.

```javascript

```

See one of our previous posts on using [Johnny-five to easily control an attached Arduino](http://blog.derivatived.com/posts/Control-Your-Robot-With-Node.js-Raspberry-PI-and-Arduino/). To summarize, two servos (steering and throttle) are maintained the by the server. Those servo structures are changed to reflect commands, and pushed to the Arduino. We sometimes test without an arduino attached, so we've added command line arguments to skip that option. Socket.io is used to provide real time communication between user and server.

```javascript
...
// allow commands to be send via http call - GET only accepts command
app.post('/command/', function (req, res) {
  processRobotCommand (req.body.command);
  
  updateRobotStatus (req.body.status);
});

// When a user connects to the server through socket.io
io.on('connection', function (socket) {
  console.log('A user has connected ');
  socket.emit('robot status', { data: 'server connected' });
  
  // Robot commands
  socket.on('robot command', function (data) {
    processRobotCommand (data.data);
  });
  
  // Status update - gets forwarded to the webpage
  socket.on('robot update', function (data) {
    var updatedData = data.data;
    updateRobotStatus (updatedData);
  });
});
...
```

The program has two communication methods: http post calls and socket.io. The http is used by the python script to send information to server.js. Socket.io is used for communication between server and web page viewer. Take a look at [Socket.io](http://socket.io/) for more information about using the library, but the server looks for webpage users that connect to it. The website provides the webpage to allow this, but in reality anyone with the proper html page and internet address can connect.

### Web Page HTML File (index.html)
We used socket.io for real time communication between the user and server. The website uses JQuery to record when the user presses buttons/move sliders and sends those actions to server.js through socket.io. The webpage can also use socket.io to listen for information from the server, such as the robot status. The look and feel was maintained with [bootstrap](http://getbootstrap.com/).


### Raspberry PI Camera Python script (raspicam.py)

* picamera
    * A python library for the Raspberry Pi camera

```python
camera.resolution = (CAMERA_WIDTH, CAMERA_HEIGHT)

start = time.time()
stream = io.BytesIO()
for foo in camera.capture_continuous(stream, 'jpeg'):
    # Write the length of the capture to the stream and flush to
    # ensure it actually gets sent
    connection.write(struct.pack('<L', stream.tell()))
    connection.flush()
    # Rewind the stream and send the image data over the wire
    stream.seek(0)
    connection.write(stream.read())
    # If we've been capturing for more than 30 seconds, quit
    if time.time() - start > 30:
        break
    # Reset the stream for the next capture
    stream.seek(0)
    stream.truncate()
```

We wanted to split up camera input and vision processing, so this script sends the image frame by frame over a socket. It expects a socket server at the address/port it's sending to.


### OpenCV Python Script (opencv.py)
We tried several communication protocols between the python scripts and node server. In the end, we decided to minimize the required libraries and settled for HTML calls.

* opencv
    * Open source computer vision library

```python
image_len = struct.unpack('<L', connection.read(4))[0]
# Construct a stream to hold the image data and read the image
# data from the connection
image_stream = io.BytesIO()
image_stream.write(connection.read(image_len))
# Rewind the stream, open it as an image with PIL and do some
# processing on it
image_stream.seek(0)
data = np.fromstring(image_stream.getvalue(), dtype=np.uint8)
image = cv2.imdecode(data, 1)
```

The script looks for an image being transferred over the socket. Then, vision algorithms can be applied to the resulting decoded image. In this case, we use a cascade classifier to find rectangles likely to contain a face.

```python
objects = cascade.detect_classifier(gray)
for (x,y,w,h) in objects:
    if (notFound):
        cv2.rectangle(after_image,(x,y),(x+w,y+h),COLOR_FOCUS,2)
        move_command(x, y, w, h)
        notFound = False
    else:
        cv2.rectangle(after_image,(x,y),(x+w,y+h),cascade.color,2)
```

We use OpenCV with cascade classification to look for upper bodies in the given images. The code actually contains cascades for other objects such as face and whole body. Then, the properties of the resulting rectangle are used to generate movement commands. For example, adjust steering based on how off-center the rectangle is. The final result is posted over http to server.js. The node server then just interprets the command like a web user button press.

Resource: tutorial used for current cascade object tracking
http://docs.opencv.org/trunk/doc/py_tutorials/py_objdetect/py_face_detection/py_face_detection.html#face-detection


##### [Comments in Discourse](http://www.sherecar.org/t/blog-post-opencv-with-an-rc-car/115)
