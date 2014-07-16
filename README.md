openvv
======

## Getting Started
* Clone the repository and cd into the root directory of it.
* Set the environment variable FLEX_HOME on your system, e.g. 

```
export FLEX_HOME=/Applications/flex_sdk_4.6
```
in your /etc/.profile or ~/.profile on Mac OS X, or Unix-like systems.  My Computer > Advanced System Properties > Environment variables on Windows systems.

* You need Apache ANT to go forward. If you don't have it, you can install it from [the official website](http://ant.apache.org/)

## Build the SWC Library and the Beacons

* Run this from the root directory of OpenVV. It will create new files `bin/openvv.swc` and `bin/OVVBeacon.swf`:

```
ant
```

* Incorporate the SWC `bin/openvv.swc` into your project
* Move the newly created `bin/Beacon.swf` to a publicly accessible web location.


###### Alternatively if you are on Mac OS X or another Unix-like platform that supports bash, you can run

```
./build.sh
```

Which has the added benefit of including the current git commit id in a trace statement so your build of OpenVV will report its version at runtime.   This is useful for "production" builds on a CI system.  

###### You can also build the components independently:

* Build the SWC Library:

```    
ant compile-lib
```

* Build the Beacons

```
ant compile-beacon
```


## Initialize OpenVV 
* Instantiate `OVVAsset` and pass the URL of your Beacon in:
```actionscript
var asset:OVVAsset = new OVVAsset('http://localhost/OVVBeacon.swf');
```
* Get viewability data at any time by calling `OVVAsset.checkViewability()`.
```actionscript
var check:OVVCheck = asset.checkViewability();
```
* Query the properties of the `OVVCheck` object to report on your player's viewability.

The properties are:

* <code>viewabilityState</code>: string Indicates whether or not the asset is viewable. Possible values are "viewable", "notViewable", "unmeasurable"
* <code>clientHeight</code>: int Current height of the client
* <code>clientWidth</code>: int Current width of the client
* <code>focus</code>: boolean Whether or not the tab and browser are in focus
* <code>objBottom</code>: int Y position of the bottom edge of the embed object
* <code>objLeft</code>: int X position of the left edge of the embed object
* <code>objRight</code>: int X position of the right edge of the embed object
* <code>objTop</code>: intY position of the top edge of the embed object
* <code>percentViewable</code>:int The percentage of the embed object's area that is calculated to be in view


3rd party integration
==========================

Allows 3rd parties to easily provide video viewability measurement by exposing the VPAID data as well as the viewability data via a JavaScript API.  The release extends the functionality of OVVAsset.as for it to be used as the base class for integrating openvv into SWF Ads.

## Demo
A working demo based on the openvv demo is available here: http://video.doubleverify.com
## Overview
The addition to OVVAsset.as includes an **initEventsWiring** and **addJavaScriptResourceOnEvent** methods to be use for third parties integrations as follow:

1.  Instantiate an OVVAsset object and pass it a unique identifier. 
2.  Registers OVVAsset.as as an event listener to VPAID events fired by the creative using **initEventsWiring** function passing the EventDispatcher that Fires IVPAID events.
3.  Call **addJavaScriptResourceOnEvent** function with the VPAID event name upon the JavaScript tag should be rendered and the JavaScript tag url.

Code snippet:
<pre>
public function initAd(width:Number, height:Number, viewMode:String, desiredBitrate:Number, creativeData:String, environmentVars:String):void 
{
   ...
  _viewabilityAsset = new OVVAsset('http://domain.com/OVVBeacon.swf', guid);  		
  // call initEventWiring with this to register asset as listener to VPAID events
  _viewabilityAsset.initEventsWiring(this); 						
  // Load 3rd party tags. Use **adID** to pass the asset ID
  var tagSrc:String = "http://someUrl.com/3rdPartyTag.js?adID=" + guid;			
  _viewabilityAsset.addJavaScriptResourceOnEvent(VPAIDEvent.AdStarted, tagSrc);
  ...
}
</pre>

### JavaScript
1.	Subscribe to events on $ovv to receive viewability/VPAID data.

Code snippet:
<pre>
$ovv.subscribe('AdLoaded', playerID, function(event, id, args) {
  	...
});
</pre>

###Understanding the data
The javascript event will receive the args {"vpaidData":vpaidData, "ovvData":ovvData}
####vpaidData:
The vpaid event data if passed from the vpaid event otherwise null.
####ovvData: 
<code>viewabilityState</code>: String Indicates whether or not the asset is viewable. Possible values are "viewable", "notViewable", "unmeasurable"
<code>clientHeight</code>: int Current height of the client
<code>clientWidth</code>: int Current width of the client
<code>focus</code>: Bolean Whether or not the tab and browser are in focus
<code>objBottom</code>: int Y position of the bottom edge of the embed object
<code>objLeft</code>: int X position of the left edge of the embed object
<code>objRight</code>: int X position of the right edge of the embed object
<code>objTop</code>: intY position of the top edge of the embed object
<code>percentViewable</code>:int The percentage of the embed object's area that is calculated to be in view
