# ArrowDB Push Notifications Library

Titanium CommonJS Library to register device for ArrowDB Push Notifications. Simply add this file to your project and follow the usage instructions below.

## Motivation
Push notifications with Titanium are sometimes a bit esoteric. With this library I'm simply trying to make it super easy to implement.

## Requirements
This library support Push Notifications via ArrowDB for iOS, Android and Blackberry. Before you use this library, you need to:

* Make sure your Titanium App is provisioned for Cloud Services.
* Obtain your Google Cloud Messaging credentials and Apple Push Notifications Certificate as explained [here](http://docs.appcelerator.com/titanium/3.0/#!/guide/Push_Notifications)
* Request BlackBerry 10 appId [docs](https://gist.github.com/pec1985/8ad59783cd5b4adc45a2), official support in 3.3.0.GA

## Installation
You could simply grab the arrowdbpush.js file and drop it into your project folder, for Titanium Classic somewhere inside **/Resources**, and for Alloy inside the **/app/lib** folder.

I'm also providing the module as an actual Module Package. To use this one, create (if you don't already have it) a modules folder in the same level as your tiapp.xml. Then copy the commonjs folder into the modules folder.

After doing this, open your tiapp.xml file, look for the modules section and add the module:

	<module platform="commonjs">com.alcoapps.arrowdbpushmod</module>

> NOTE: the version attribute is optional for example <module platform="foo" version="1.0.x">bar</module>

## Usage

```js
// Set android-only options
var androidOptions = {
    focusAppOnPush: false,
    showAppOnTrayClick: true,
    showTrayNotification: true,
    showTrayNotificationsWhenFocused: false,
    singleCallback: true
};

// Set ios-only options.
if (Ti.Platform.name == "iPhone OS") {
    // Sets interactive notifications as well if iOS8 and above. Interactive notifications is optional.
    if (parseInt(Ti.Platform.version.split(".")[0]) >= 8) {
        var thumbUpAction = Ti.App.iOS.createUserNotificationAction({
            identifier: "THUMBUP_IDENTIFIER",
            title: "Agree",
            activationMode: Ti.App.iOS.USER_NOTIFICATION_ACTIVATION_MODE_BACKGROUND,
            destructive: false,
            authenticationRequired: false
        });

        var thumbDownAction = Ti.App.iOS.createUserNotificationAction({
            identifier: "THUMBDOWN_IDENTIFIER",
            title: "Disagree",
            activationMode: Ti.App.iOS.USER_NOTIFICATION_ACTIVATION_MODE_BACKGROUND,
            destructive: false,
            authenticationRequired: false
        });

        var thumbUpDownCategory = Ti.App.iOS.createUserNotificationCategory({
            identifier: "THUMBUPDOWN_CATEGORY",
 	        // The following actions will be displayed for an alert dialog
            actionsForDefaultContext: [thumbUpAction, thumbDownAction],
            // The following actions will be displayed for all other notifications
            actionsForMinimalContext: [thumbUpAction, thumbDownAction]
        });

        var iosOptions = {
            types: [
                Ti.App.iOS.USER_NOTIFICATION_TYPE_ALERT,
                Ti.App.iOS.USER_NOTIFICATION_TYPE_SOUND
            ],
            categories: [thumbUpDownCategory]
        };
    }  else {
        // No support for interactive notifications, omit categories
        var iosOptions = {
            types: [
                Ti.App.iOS.USER_NOTIFICATION_TYPE_ALERT,
                Ti.App.iOS.USER_NOTIFICATION_TYPE_SOUND
            ]
        };  
    }
}

// Set blackberry-only options
var blackberryOptions = {
    appId: "4427-7h6l37627mrr0I3956a74om7643M17l7921",
    ppgUrl: "http://cp4427.pushapi.eval.blackberry.com",
    usePublicPpg: true,
    launchApplicationOnPush: true
};

// Set cross-platform event
var onReceive = function(evt) {
    alert('A push notification was received!');
    console.log('A push notification was received!' + JSON.stringify(evt));
};

// Set android-only event
var onAndroidLaunched = function(evt) {
    alert('A push notification was received - onAndroidLaunched');
    console.log('A push notification was received!' + JSON.stringify(evt));
};

// Set android-only event
var onAndroidFocused = function(evt) {
    alert('A push notification was received - onAndroidFocused');
    console.log('A push notification was received!' + JSON.stringify(evt));
};

// Load library
var ArrowDBPushProvider = require('arrowdbpush');

// Create instance with your own or the user's username and password
var ArrowDBPush = new ArrowDBPushProvider.ArrowDBPush('your_acs_admin_uid','your_acs_admin_pwd');

// or make it as guest
// var ArrowDBPush = new ArrowDBP.ArrowDBPush();

// Set the channel to subscribe to
var channel = 'All users';

// Register this device
ArrowDBPush.registerDevice(channel, onReceive, iosOptions, androidOptions, blackberryOptions, onAndroidFocused, onAndroidLaunched);

// Unregister this device
// ArrowDBPush.unsubscribeFromChannel(channel, token, onSuccess, onFail);
```

## Sending messages to your subscribers

### Using the ArrowDB Dashboard

To send to your iOS or Android registered devices simply log on to your ArrowDB Dashboard, go to the Push Notifications Tab, fill out the screen and send.

![acsiosandroid](http://s27.postimg.org/5ixtazxwz/Screen_Shot_2014_03_31_at_11_51_28_AM.png)

For more information on interactive notifications, refer to:
http://docs.appcelerator.com/titanium/latest/#!/guide/iOS_Interactive_Notifications

### Sending to Blackberry subscribers

To send to your Blackberry registered devices, you can use [this node.js script](https://github.com/pec1985/BB10-Push-Server) created by [Pedro Enrique](https://github.com/pec1985).

### Sending from other apps or creating your own console

If you wish to send Push Notifications from other apps, or as a result of an operation on your website or back-end service, you can use any of these scripts:

* PHP : [https://github.com/ricardoalcocer/acsphppushnotifications](https://github.com/ricardoalcocer/acsphppushnotifications)
* PERL : [http://ulizama.com/2014/05/using-perl-to-send-acs-push-notifications/](http://ulizama.com/2014/05/using-perl-to-send-acs-push-notifications/)
* .NET : [https://github.com/Xadeu/ArrowDBPushNotifications](https://github.com/Xadeu/ArrowDBPushNotifications)

## Credits
This module is based on code by my buddy [Pablo Rodríguez](https://github.com/pablorr18), now with some additional sugar and converted into a reusable CommonJS Module.

BlackBerry 10 support by [Hazem Khaled](http://github.com/hazemkhaled)

## License
Licensed under the terms of the [MIT License](alco.mit-license.org)
