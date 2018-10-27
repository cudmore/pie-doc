## Introduction

The PiE server provides a REST interface allowing web URLs to get and set configuration parameters and to perform actions like starting and stopping recording.

For example, if your Pi has the IP address 192.168.4, the status of the PiE server can be retreived with:

	http://192.168.1.4:5010/status

The entire [web interface](web-interface.md) is driven by calling this REST interface from Javascript in [pie/pie_app/static/treadmill.js](https://github.com/cudmore/pie/blob/master/pie_app/static/treadmill.js).

You can use this REST interface to control the PiE server from Python or Matlab or make your own web interface using Javascript.

## Endpoints

The following is a list of valid endpoints.
	
### Get

	/status

	/systeminfo

	/api/refreshsysteminfo

	/log

	/environmentlog

	/api/lastimage


### Set

	/api/set/animalid/<string:animalID>

	/api/set/conditionid/<string:conditionID>

	/api/set/scopefilename/<string:scopeFilename>

### Actions

	/api/action/<string:thisAction>

| thisAction	| action
| -----			| -----
|startRecord	|
|stopRecord		|
|startStream	|
|stopStream		|
|startArm		|
|stopArm		|
|startArmVideo	|
|stopArmVideo	|

### Submit/Post

This is a POST endpoint to change any configuration parameter. Need to send POST data which is results or modified version of /status.

	/api/submit/<string:submitThis>

| thisAction	| action
| -----			| -----
|saveconfig		| Save current configuration (no post data)
|configparams	|
|pinparams 		|
|animalparams	|
|ledparams		|
|motorparams	|

	/api/submit/loadconfig/<string:loadThis>

## Scripting with the REST interface

### Python

In this example, we get the server status, toggle the white LED, and post this new data to change the white LED on the server.

1) Install requests if necessary

	pip3 install requests
	
2) Put this code into a file called 'testrest.py'

```
import requests
from pprint import pprint

# get the current status
url = 'http://192.168.1.4:5010/status'
r = requests.get(url)

status = r.json()
status = status['trial']['config']

# toggle the white LED
# we are hard coding [1] here, IR LED is [2]
whiteLED = status['hardware']['eventOut'][1]['state']
whiteLED = not whiteLED
status['hardware']['eventOut'][1]['state'] = whiteLED

# post the changes
url = 'http://192.168.1.4:5010/api/submit/ledparams'
r = requests.post(url, json = status) 

# check that our changes were returned (meaning they were set on the server)
status = r.json()
status = status['trial']['config']
print('server whiteLED is now:', status['hardware']['eventOut'][1]['state'])

# check the last response from the server
print('last response: ')
pprint(r.json()['trial']['runtime']['lastResponse'])
```

3) Run on command line

	python3 testrest.py

Each time this script is run, the white LED will be toggled.
	
### Matlab

Coming soon!

### [Processing][processing]

[Processing][processing] is a healthy alternative to Matlab and/or Python as it allows many of the same functionality without the overhead of a complicated, large, and proprietary installation.

The Processing code below will fetch the status of the PiE server, toggle both the white and IR LEDs and then set new values on the PiE server.



```
// This code assumes that http.request library is installed
// To install, select menu 'Sketch - Import Library... - Add Library...' and search for 'http requests'
import http.requests.GetRequest;

  // search and replace 192.168.1.4 with the IP address of a PiE server

  // grab the PiE server 'status' using endpoint /status
  JSONObject status = loadJSONObject("http://192.168.1.4:5010/status");
  
  // (0) parse the PiE server 'status' into its different pieces
  JSONObject trial = status.getJSONObject("trial");
  JSONObject config = trial.getJSONObject("config");
  JSONObject hardware = config.getJSONObject("hardware");
  JSONArray eventOut = hardware.getJSONArray("eventOut"); // eventOut is a JSON array
  
  // specify the white and IR LED indices (these are indices into JSON eventOut array)
  Integer whiteIdx = 1;
  Integer irIdx = 2;
  
  // (1) get the the initial 'state' of white and IR LEDs
  JSONObject whiteLED = eventOut.getJSONObject(whiteIdx);
  JSONObject irLED = eventOut.getJSONObject(irIdx);
  println("1");
  println("whiteLED 'state' was:", whiteLED.getBoolean("state"));
  println("irLED 'state' was:", irLED.getBoolean("state"));

  // (2) toggle/invert the 'state' of white and IR leds
  Boolean newWhiteState = ! whiteLED.getBoolean("state");
  Boolean newIRState = ! irLED.getBoolean("state");
  println("2");
  println("newWhiteState:", newWhiteState);
  println("newIRState:", newIRState);
  
  // convert from Boolean to int
  // endpoint /api/v2/set/led/<ledIdx>/<value> expects <value> in (0,1) and not in (true, false)
  int newWhiteStateInt = newWhiteState ? 1 : 0;
  int newIRStateInt = newIRState ? 1 : 0;

  // set the new values on the PiE server using REST endpoint /api/v2/set/led/<ledIdx>/<value>
  // where:
  //    <ledIdx>: 1 for white, 2 for IR (e.g. whiteIdx and irIdx)
  //    <value>: 1 for on, 0 for off
  GetRequest postWhite = new GetRequest("http://192.168.1.4:5010/api/v2/set/led/" + whiteIdx + "/" + newWhiteStateInt);
  postWhite.send();
  GetRequest postIR = new GetRequest("http://192.168.1.4:5010/api/v2/set/led/" + irIdx + "/" + newIRStateInt);
  postIR.send();

  // (3) grab the PiE server 'status' using endpoint /status again to ensure our changes took effect
  // You can also look at the web interface to see if the LED changes took effect !!!
  status = loadJSONObject("http://192.168.1.4:5010/status");
  
  // parse the PiE server 'status' into its different pieces, the same as step (0) above
  trial = status.getJSONObject("trial");
  config = trial.getJSONObject("config");
  hardware = config.getJSONObject("hardware");
  eventOut = hardware.getJSONArray("eventOut");
  whiteLED = eventOut.getJSONObject(whiteIdx);
  irLED = eventOut.getJSONObject(whiteIdx);
  
  println("3");
  println("whiteLED 'state' is now:", whiteLED.getBoolean("state"));
  println("irLED 'state' is now:", irLED.getBoolean("state"));
```

[processing]: http://processing.org