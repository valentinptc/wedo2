# wedo 2.0

This is a node.js module for the Lego WeDo 2.0 set.


+ Version 1.5 has significant changes to 1.1
+ Version 1.5.5 has new sensor ranges. 
The tilt sensor output is in degree and distance sensor is in cm.
+ Version 1.5.6 has a new initialisation method, to support the name search.


Install

~~~~shell
npm install wedo2
~~~~


#### How to initialize a Wedo block

Once the wedo2 is loaded, the module starts searching for WeDo's
~~~~js
var Wedo2 = require('WeDo2');
var wedo2 = new Wedo2();
~~~~

If you want to search for a specific range of WeDo's, you can add parts of their names as argument.
The following example will search for WeDo's that all have "lego" as part of their name.

~~~~js
var Wedo2 = require('WeDo2');
var wedo2 = new Wedo2("lego");
~~~~

#### Events

All events emit the uuid from the WeDo they have been placed from.
The uuid is always the last argument.

If a new WeDo is connected it emits the "connected" event.

~~~~js
wedo2.on('connected', function (uuid) {
    console.log('I found a WeDo with uuid: '+uuid);
    // Place getters and setters in here, to make sure that they are called,
    // when the object is connectged
});
~~~~

If a new WeDo is disconnected it emits the "connected" event.
~~~~js
wedo2.on('disconnected', function (uuid) {
    console.log('I removed a WeDo with uuid: '+uuid);
});
~~~~

Battery status in %. uuid tells on which WeDo the status was emitted.
~~~~js
wedo2.on('battery', function (status, uuid) {
    console.log('Battery: ' + status + '% @ '+uuid);
});
~~~~

If a distance sensor is connected, it will send its 
distance in the range of 0 and 10 (matching cm scale) as well the port.
~~~~js
wedo2.on('distanceSensor', function (distance, port, uuid) {
	console.log('distanceSensor: '+distance+' at port '+port + ' @ '+uuid);
});
~~~~

If a tilt sensor is connected, it will send its 
tilt x and y in the range of -45 and 45 as well the port.
~~~~js
wedo2.on('tiltSensor', function (x,y, port, uuid) {
    console.log('tilt sensor: '+x+'   '+y+' at port '+port +' @ '+uuid);
});
~~~~

If the WeDo button on the controller is clicked the following event is fired.
~~~~js
wedo2.on('button', function (button, uuid) {
	console.log('button state: '+button + ' @ '+ uuid );
});
~~~~

Every time a sensor or motor is connected and disconnected the port event is fired.
~~~~js
wedo2.on('port', function (port, connected, type, uuid) {
	if(connected){
		console.log('Found '+type+' on port '+port+ ' @ '+ uuid );
	} else {
		console.log('Disconnected '+type+' on port '+port+ ' @ '+ uuid );
	}
});
~~~~

#### Setters

Without a uuid argument, all setters will set values for the first WeDo found.
If you use more then one wedo you can reach the specific device via the uuid argument with the following methods.

<b>uuid</b>: You can hand over the exact uuid of an object.<br>
<b>name</b>: Add the exact name of your WeDo instead of the uuid. If two objects have the same name, the first match will count.<br>
<b>number</b>: Add a number (0,1,2,...) instead of the uuid to set different devices.

Set the name of your WeDo within the device. This name will be saved in your WeDo device until you rename it again. If you use more then one device, this a good place to define names to differentiate specific WeDo's.
~~~~js
wedo2.setDeviceName(yourName, (optional) uuid);
~~~~

Set the Led color of the WeDo controller to an RGB value.
Each value is in the scale from 0-255.
For example Red, Green Blue all set to 255 is white:
~~~~js
wedo2.setLedColor(r,g,b, (optional) uuid); 
~~~~

Set the motor speed, if a motor is connected.<br>
(Optional) If you want to operate a motor on a specific port,
you can add the port number (1 or 2) after the speed.
Set the port to ```null``` to leave it blank in case you want to set device.
~~~~js
wedo2.setMotor(speed, (optionl) port, (optional) uuid);
~~~~

Play a sound on the build in piezo speaker.
The frequency of the sound is in kHz and the length is in ms.

~~~~js
wedo2.setSound(frequency, length, (optional) uuid)
~~~~


#### Getters
		
If you work with more then one WeDo, you have the same uuid choices (nothing, uuid, name, number) as with the setters.		
		
To get the name of your WeDo
~~~~js
wedo2.getDeviceName(function(name, uuid){
    console.log("the device name is "+name+" @ "+uuid);
}, uuid);
~~~~

Get the Signal strength of the WeDo Bluetooth LE.

~~~~js
wedo2.getSignalStrength(function (err, signal, uuid) {
	console.log('Signal: ' + signal + 'dBm'+ " @ "+uuid);
}, uuid);
~~~~

#### Other interesting things
	
Each WeDo is saved in an object reachable via:

~~~~js
wedo2.wedo
~~~~

In this object new WeDo's are saved with their uuid as key for the time that they are connected.
If you know the uuid of your WeDo, you can test its connection like so: 
~~~~js
if(wedo2.wedo[uuid])
~~~~

If you only know the name of the WeDo or just a number in which order it was discovered (the first Wedo will always have the number 0),
then you can redrive the uuid with the following function. If no WeDo uuid has been found, the response will be ```null``

~~~~js
var uuid = wedo2.getUuidFromInput(input)
~~~~

Once you know that it is connected, you can read all kinds of stuff:

Name
~~~~js
wedo2.wedo[uuid].name
~~~~

Type of connected items on ports
~~~~js
wedo2.wedo[uuid].port[0].type
~~~~

And so on.
