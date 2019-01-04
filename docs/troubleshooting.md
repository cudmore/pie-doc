The first thing to check is the PiE server log. The log can be viewed in many ways,

 1. From the web interface
 2. By running the PiE server on the command line with `./pie run`
 3. By directly viewing the log file with `more ~/pie/pie_app/pie.log`


## Common Errors

### OSError: [Errno 98] Address already in use

This happens when you try and start the server but it is already running. Usually when it is running in the background and you run it again with `./pie run`. Just stop the server with `./pie stop` and then try again with `./pie run`.


## Troubleshooting the camera

Capture a still image with the Pi camera with:

```
raspistill -o test.jpg
```

If you get any errors then there is a problem with the Pi Camera.

Make sure the Pi Camera is activated.

```
# type this at a command prompt
sudo raspi-config

# select '5 Interface Options'
# select 'P1 Camera'
# Answer 'Yes' to question 'Would you like the camera interface to be enabled?'
```

### bCamera PiCameraMMALError: Failed to enable connection: Out of resources

If you receive this error in the web interface or PiE server log, it means the camera is in use by some other process. The Raspberry camera can only do one thing at a time, it can stream or record but not both at the same time. In addition, the camera can not record (or stream) in two different programs simultaneously.

Make sure other programs are not using the camera and try again. Rebooting with 'sudo reboot' usually does the trick unless these programs, like the PiE server, are set up to run at boot.


## Troubleshooting a DHT temperature/humidity sensor

Run the simplified code in the [testing/](testing/) folder. If you can't get a temperature/humidity reading with this code, it will not work within the PiE server.

Check the PiE server log (see above) and make sure the Adafruit DHT driver is installed and run when the PiE server is started. You should see entries in the PiE server log like this:

	[2018-10-14 09:53:59,596] {bTrial.py <module>:51} DEBUG - Loaded Adafruit_DHT
	[2018-10-14 09:54:00,168] {bTrial.py __init__:178} DEBUG - starting temperature thread
	[2018-10-14 09:54:00,172] {bTrial.py tempThread:918} INFO - tempThread() sensorTypeStr:AM2302 sensorType:22 pin:4

If the DHT driver is not installed, install it with `./install-dht`, restart the PiE server with `./pie restart`, and check the PiE server log again.

## Converting video to mp4

The PiE server uses [libav (avconv)][libav] to convert video from .h264 to .mp4. If libav (avconv) does not install during `~/pie/install-pie`, this conversion will not work.

## Troubleshooting uv4l streaming

In rare instances the [uv4l][uv4l] streaming server does not stop properly. Streaming with uv4l runs at the system level and not in Python. As such, uv4l needs to be controlled via the command line.

```
#list all uv4l processes
ps -aux | grep uv4l

# will yield something like
root     23117  9.8  1.3 140796 12312 ?        Ssl  20:34   0:02 uv4l --driver raspicam --auto-video_nr --encoding h264 --width 640 --height 480 --enable-server on
pi       23262  0.0  0.1   6644  1316 pts/1    S+   20:34   0:00 grep --color=auto uv4l

# kill uv4l with its process id (PID)
# '--' is needed to kill parent and child processes
sudo kill -- 23117

#check the uv4l process is no longer running
ps -aux | grep uv4l

# should yield
pi         674  0.0  0.1   6644  1320 pts/0    S+   21:06   0:00 grep --color=auto uv4l
```

#### uv4l will not go away

Worst case senario is `ps -aux | grep uvl4` yields something like this

```
root     23117 17.2  0.0      0     0 ?        Zsl  20:34   0:37 [uv4l] <defunct>
```

If you see the `<defunct>` then restart the Pi with `sudo reboot` and it should be fixed.

## Manually editing user config (json) files

The PiE server comes with three default sets of options: Homecage, Scope, and Treadmill. There is an additional `User` configuration that can be edited manually to configure the PiE server.

To edit the `User` file, open `pie/pie_app/config/config_user.json` in the [pie/pie_app/config/](config-folder) folder. The format of the file is [json][json] and basically specifies key/value pairs. Do not add or remove any keys, just change their values. The json format is very strict, if there are any syntax errors, the file will not load and the PiE server will not run.

To check your work, use

```
cd ~/pie/pie_app/config
cat config_user.json | python -m json.tool
```

If your edits are syntatically correct, this command will output the contents of the file. If you have created an error, they will be reported on the command line. For example, if your forget a comma  after `"enabled": true` like this

```
        "triggerIn": {
            "enabled": true
            "pin": 23,
            "polarity": "rising",
            "pull_up_down": "down"
        },
```

You will get an error

```
Expecting , delimiter: line 27 column 13 (char 648)
```


## Checking system and software versions

The PiE server should work out of the box but as system and Python libraries are updated, things can potentially become broken. Thus, it is important to check and verify a number of system and Python Package versions. Here is a snapshot of versions for a working PiE server as of October 2018. As python packages are updated, things can potentially break.

```
=== linux:
('debian', '9.4', '')
Linux hc4 4.14.71-v7+ #1145 SMP Fri Sep 21 15:38:35 BST 2018 armv7l GNU/Linux
=== python: 3.5.3 (default, Jan 19 2017, 14:11:04) [GCC 6.3.0 20170124]
=== PiE: 20190104a
=== picamera: [picamera 1.13 (/home/pi/pie/pie_env/lib/python3.5/site-packages)]
=== RPi.GPIO: 0.6.3
=== serial: 3.4
=== flask: 1.0.2
=== flask_cors: 3.0.6
=== flask_socketio: 3.0.2
=== avconv:
ffmpeg version 3.2.10-1~deb9u1+rpt2 Copyright (c) 2000-2018 the FFmpeg developers
built with gcc 6.3.0 (Raspbian 6.3.0-18+rpi1+deb9u1) 20170516
configuration: --prefix=/usr --extra-version='1~deb9u1+rpt2' --toolchain=hardened --libdir=/usr/lib/arm-linux-gnueabihf --incdir=/usr/include/arm-linux-gnueabihf --enable-gpl --disable-stripping --enable-avresample --enable-avisynth --enable-gnutls --enable-ladspa --enable-libass --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libebur128 --enable-libflite --enable-libfontconfig --enable-libfreetype --enable-libfribidi --enable-libgme --enable-libgsm --enable-libmp3lame --enable-libopenjpeg --enable-libopenmpt --enable-libopus --enable-libpulse --enable-librubberband --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libssh --enable-libtheora --enable-libtwolame --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx265 --enable-libxvid --enable-libzmq --enable-libzvbi --enable-omx-rpi --enable-mmal --enable-openal --enable-opengl --enable-sdl2 --enable-libdc1394 --enable-libiec61883 --arch=armhf --enable-chromaprint --enable-frei0r --enable-libopencv --enable-libx264 --enable-shared
libavutil 55. 34.101 / 55. 34.101
libavcodec 57. 64.101 / 57. 64.101
libavformat 57. 56.101 / 57. 56.101
libavdevice 57. 1.100 / 57. 1.100
libavfilter 6. 65.100 / 6. 65.100
libavresample 3. 1. 0 / 3. 1. 0
libswscale 4. 2.100 / 4. 2.100
libswresample 2. 3.100 / 2. 3.100
libpostproc 54. 1.100 / 54. 1.100

=== uv4l
Userspace Video4Linux
Copyright (C) Luca Risolia 
Version 1.9.16 built on Oct 6 2018
```

### Version check using the rest interface

With a PiE server running, browse to this url:

	http://[IP]:5010/version
	
### Version check from the command line

```
cd ~/pie
source pie_env/bin/activate # activate the Python3 virtual environment
python version_check.py # run version check
```

### Other versions to check

```
cat /etc/os-release
```

```
# returns
PRETTY_NAME="Raspbian GNU/Linux 9 (stretch)"
NAME="Raspbian GNU/Linux"
VERSION_ID="9"
VERSION="9 (stretch)"
ID=raspbian
ID_LIKE=debian
```

```
uname -a
```

```
# returns
Linux pi15 4.14.52-v7+ #1123 SMP Wed Jun 27 17:35:49 BST 2018 armv7l GNU/Linux
```

[uv4l]: https://www.linux-projects.org/uv4l/
[libav]: https://libav.org/
[config-folder]: https://github.com/cudmore/pie/tree/master/pie_app/config
