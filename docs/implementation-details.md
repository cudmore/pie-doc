Once running, the PiE server should be simple to use and only requires interaction with its [web interface](web-interface.md). Yet, for this simple system to work, a number of independent components must each work and must communicate with each other. As is often the case, the details in creating a simple system is complex.

What follows is a description of the different pieces of the PiE server with links to the source code and the components that are used.

## Raspberry Pi

At its core, this system requires a functioning Raspberry Pi running the [Raspbian][raspbian] operating system. Because this is an intentionally stripped down system, some things are not installed out of the box like [mounting a USB drive](mount-usb-drive.md). On the other hand, this is a full-fledged [Debian Linux][debian] system so you can activate powerful features such as a [file-server](file-server.md).

## Back End

### Web server

 - [pie_app/treadmill_app.py][treadmill_app.py] - This code creates the main PiE web server (running on port 5010) using [Flask][flask] and is the main starting point to run the PiE server. It acts as a connection between web requests (URLs) and the backend by processing each incoming URL and calling the appropriate backend code in [pie_app/treadmill.py][treadmill.py].

### Python class library

 - [pie_app/treadmill.py][treadmill.py] - Wrapper class around [pie_app/bTrial.py][bTrial.py] to implement specifics of the PiE server beyond what bTrial provides. This mainly includes some logic using web sockets, in particular [flask-socketio][flask-socketio], for bi-directional communication from the browser to the backend and from the backend to the browser.

 - [pie_app/bTrial.py][bTrial.py] - Main catch-all class that implements the logic of a trial based experiment. When it is instantiated, it creates a number of background threads to control independent processes including: GPIO pins, the timing of automatic lights, temperature/humidity readings, and serial communication with a Teensy. The use of background threads is important, they are run in parallel to the main code (utilizing the Raspberry Pis 4-core CPU) and allow everything to run smoothly with minimal [blocking][blocking-wikipedia]. Once running, bTrial receives commands from the web interface in treadmill_app.py to take actions like: changing/loading/saving user configurations, starting/stopping video recording and streaming, setting GPIO pins, and performing serial communication with a Teensy.
 
 - [pie_app/bCamera.py][bCamera.py] - Code to control a Raspberry Pi camera using the [piCamera][piCamera] Python library. This is where both video recording and streaming with uv4l is started and stopped. This also contains a background thread that uses avconv to convert .h264 video saved by the camera into .mp4.
 
 - [pie_app/bPins.py][bPins.py] - Code to control the Raspberry Pi GPIO pins using either [RPi.GPIO][RPi.GPIO] or the [pigpio][pigpio] daemon. This is where the PiE server can change GPIO pins and where code is triggered when a pin changes from an external source like a microscope.
 
 - [pie_app/bSerial.py][bSerial.py] - Code to communicate with an Arduino/Teensy microcontroller over a serial connection (via a USB cable). This is basically to get and set parameters as well as starting/stopping a trial on the Teensy. It works as a background thread instantiated by bTrial and then sits silently waiting for commands to be inserted into a queue. This is not used to program the Teensy, that is handled by [platformio][pie/platformio]
 
 - [pie_app/bUtil.py][bUtil.py] - Implements basic utility functions like fetching the system date/time, determining the amount of drive space remaining, and getting the IP address and hostname.
 
 - [pie_app/config][pie_app/config] - Text files written in [json][json] that provides human editable configuration options for the server. These json files are loaded when the server starts and are saved when the user saves from the web interface. These json files contain all the configurable options provided by the PiE server, things like the default state of GPIO pins and configurations like the video recording duration and number of repeats.
 
## Front End

The backend web server in [pie_app/treadmill_app.py][treadmill_app.py] delivers .html pages which have logic driven by Javascript (.js) and formatted with .css.

 - [pie_app/templates/index.html][pie_app/templates/index.html] - Main web interface written in [html][html], uses [Javascript][javascript] and the Javascript [Angular][angular] library to provide dynamic and real-time content.
 
 - [pie_app/static/treadmill.js][pie_app/static/treadmill.js] - [Javascript][javascript] code that implementes the logic of the web interface. Has functions that get called when user is clicking or entering parameter values. Also has function to talk to [treadmill_app.py][treadmill_app.py] web server via a [REST][rest] interface. This is not standard Javascript but is using the [Angular][angular] library.
 
 - [pie_app/static/treadmill.css][pie_app/static/treadmill.css] - A [Cascading Style Sheet (css)][css] text file that describes the precise visual layout of the web pages.

 - Miscellaneous - Each webpage served by the PiE server needs a .html file and often uses a Javascript .js file to control the logic of user interaction. This includes [videolist.html][videolist.html] which serves a list of video files saved on the Pi and [environment.html][environment.html] along with [environment.js][environment.js] which controls the web based plotting of temperature and humidity.
 
## Admin server

The [admin_app][pie/admin_app] contains a second web server (running on port 5011) allowing the main PiE server (running on port 5010) to be restarted and the Raspberry Pi to be rebooted. This is necessary to allow all this to happen from the PiE [web interface](web-interface.md). This admin app is automatically installed when the PiE server is installed with `./install-pie` and should not need further tweaking.

## Commander server

The [commander_app][pie/commander_app] creates a web interface (running on port 8000) to control any number of PiE servers (on port 5010) running on different Raspberry Pis. The commander server runs completely independent of the PiE server and can be installed on its own dedicated Raspberry Pi or one that is already running the PiE server. See the [commander](commander.md) documentation for more details.

**Note..** In a near future version, the commander will be able to run on any other machine including macOS and Microsoft Windows computers.

## Components

### uv4l

The [uv4l][uv4l] system is used to streaming video from the camera to a web browser. This is a system wide program and is controlled from bCamera using bash script in [pie_app/bin][pie_app/bin].


### avconv

The [avconv][avconv] package (also known as libav) is used to convert .h264 files saved by the camera to .mp4. This is a system wide program and is controlled from bCamera using bash script in [pie_app/bin][pie_app/bin].

### DHT sensors

Readings from AM2302 and DHT22 temperature/humidity sensors requires the Adafruit Python library [Adafruit_Python_DHT][Adafruit_Python_DHT]. Readings are implemented in a background task in bTrial.

## Documentation

Last but not least, this website is written in [markdown][markdown]. The markdown and a description of the contents using [yaml][yaml] are brought together, compiled, and a static site is generated by [mkdocs][mkdocs]. The final layout is controlled by an mkdocs theme called [material][material-theme]. All of this is then pushed to [Github][github] which serves this website fast and reliable.


[raspbian]: https://www.raspberrypi.org/documentation/raspbian/
[debian]: https://www.debian.org/

[flask]: http://flask.pocoo.org/
[flask-socketio]: https://flask-socketio.readthedocs.io/en/latest/

[markdown]: https://en.wikipedia.org/wiki/Markdown
[mkdocs]: http://www.mkdocs.org/
[yaml]: http://yaml.org/
[material-theme]: https://squidfunk.github.io/mkdocs-material/

[html]: https://en.wikipedia.org/wiki/HTML
[javascript]: https://www.javascript.com/
[angular]: https://angularjs.org/

[rest]: https://en.wikipedia.org/wiki/Representational_state_transfer

[javascript]: https://www.javascript.com/
[css]: https://www.w3schools.com/css/css_intro.asp

[json]: https://www.json.org/

[treadmill_app.py]: https://github.com/cudmore/pie/blob/master/pie_app/treadmill_app.py
[treadmill.py]: https://github.com/cudmore/pie/blob/master/pie_app/treadmill.py
[bTrial.py]: https://github.com/cudmore/pie/blob/master/pie_app/bTrial.py
[bCamera.py]: https://github.com/cudmore/pie/blob/master/pie_app/bCamera.py
[bPins.py]: https://github.com/cudmore/pie/blob/master/pie_app/bPins.py
[bSerial.py]: https://github.com/cudmore/pie/blob/master/pie_app/bSerial.py
[bUtil.py]: https://github.com/cudmore/pie/blob/master/pie_app/bUtil.py
[pie_app/config]: https://github.com/cudmore/pie/tree/master/pie_app/config

[videolist.html]: https://github.com/cudmore/pie/blob/master/pie_app/templates/videolist.html

[environment.html]: https://github.com/cudmore/pie/blob/master/pie_app/templates/environment.html

[environment.js]: https://github.com/cudmore/pie/blob/master/pie_app/static/environment.js

[pie_app/templates/index.html]: https://github.com/cudmore/pie/blob/master/pie_app/templates/index.html

[pie_app/static/treadmill.js]: https://github.com/cudmore/pie/blob/master/pie_app/static/treadmill.js

[pie_app/static/treadmill.css]: https://github.com/cudmore/pie/blob/master/pie_app/static/treadmill.css

[picamera]: https://picamera.readthedocs.io
[RPi.GPIO]: https://pypi.org/project/RPi.GPIO/
[pigpio]: http://abyz.me.uk/rpi/pigpio/

[pie/platformio]: https://github.com/cudmore/pie/tree/master/platformio

[pie_app/bin]: https://github.com/cudmore/pie/tree/master/pie_app/bin

[uv4l]: https://www.linux-projects.org/uv4l/
[avconv]: https://libav.org/avconv.html

[Adafruit_Python_DHT]: https://github.com/adafruit/Adafruit_Python_DHT

[github]: http://github.com

[pie/admin_app]: https://github.com/cudmore/pie/tree/master/admin_app
[pie/commander_app]: https://github.com/cudmore/pie/tree/master/commander_app

[blocking-wikipedia]: https://en.wikipedia.org/wiki/Blocking_(computing)