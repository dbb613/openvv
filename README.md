Getting Started
===
This is a guide to help you get OpenVV integrated into your player or creative using AS3.

Get the code
---
Clone the repo

    git clone git@github.com:openvv/openvv.git

Copy the source into your project

    cp -r openvv/src/org <project>/org

Import
---
Import the AS3 interface library.  OpenVV will raise events when viewable or discernible impressions occur.  Import the OpenVV events library to access these events

    import org.openvv.OVVAsset;
    import org.openvv.events.OVVEvent;

Instantiate and initialize
---
If you want to check viewability on demand, you'll want to store your instance of OVVAsset as an instance variable

    private var oAsset:OVVAsset;

Create a new instance and register event listeners for viewable and discernible impression events

    oAsset = new OVVAsset();
    oAsset.addEventListener(OVVEvent.OVVImpression, onViewableImpression);
    oAsset.addEventListener(OVVEvent.OVVDiscernibleImpression, onDiscernibleImpression);

At this point OpenVV will attempte to load the required javascript to the DOM.  If ExternalInterface is unavailable the `oAsset.results` property will contain an error property set to "ExternalInterface not available," otherwise it will contain the viewability state at the time of construction.  

Getting data
-----
OpenVV will now start monitoring the viewability state of the embedded swf.  The first time the swf is in view for 1 consecutive second, `OVVDiscernibleImpression` will be dispatched.  The first time the swf is in view for 5 consecutive seconds, `OVVImpression` will be dispatched.

You can request the viewability state of your swf on demand at any time once OpenVV has been instantiated

    var obj:Object = oAsset.checkViewability();

For example, if you want to check viewability on quartiles, you would call `checkViewability()` when your swf detects quartile events.

Understanding the data
---
The results of a vieability check are returned as an `Object`.  The returned object will either contain an `error` property or a set of data points.  The data properties are as follows:

`vieabilityState:String` Indicates whether or not the asset is viewable.  Possible values are "viewable", "notViewable", "unmeasurable"  
`clientHeight:int` Current height of the client  
`clientWidth:int` Current width of the client  
`focus:Bolean` Whether or not the tab and browser are in focus  
`objBottom:int` Y position of the bottom edge of the embed object  
`objLeft:int` X position of the left edge of the embed object  
`objRight:int` X position of the right edge of the embed object  
`objTop:int`Y position of the top edge of the embed object  
`percentViewable:int` The percentage of the embed object's area that is calculated to be in view  
