# Cordova plugin for [Firebase](https://firebase.google.com)

This plugin is mix of https://github.com/arnesson/cordova-plugin-firebase and https://github.com/chemerisuk/cordova-plugin-firebase-dynamiclinks. There were done some changes to both of the plugins so it works correctly on OutSystems Mobile Apps.

You can check the available API in [here](https://github.com/OutSystemsExperts/cordova-plugin-firebase/blob/v1.0.0-os/docs/API.md) and [here](https://github.com/OutSystemsExperts/cordova-plugin-firebase-dynamiclinks/tree/v1.0.0-os). The methods names are the same and you can use the API as in their docs. But for simplicity, you call the method calling `Firebase.method()`.

For example, instead of:
```
FirebasePlugin.getToken(function(token) {
    // save this server-side and use it to push notifications to this device
    console.log(token);
}, function(error) {
    console.error(error);
});
``` 
You can call: 
```
Firebase.getToken(function(token) {
    // save this server-side and use it to push notifications to this device
    console.log(token);
}, function(error) {
    console.error(error);
});
``` 

## Index

<!-- MarkdownTOC levels="2" autolink="true" -->

  - [Index](#index)
  - [Plugin Explanation](#plugin-explanation)
  - [Supported Platforms](#supported-platforms)
  - [Installation](#installation)
  - [Notification Action Buttons](#notification-action-buttons)

<!-- /MarkdownTOC -->

## Plugin Explanation

This plugin has as dependencies the cordova-plugin-firebase, for the usage of Firebase Analytics, Firebase Cloud Messaging and many more and cordova-plugin-firebase-dynamiclinks

## Supported Platforms
- iOS
- Android
  
## Installation

$ cordova plugin add outsystems-cordova-plugin-firebase-with-dynamiclinks --variable APP_DOMAIN="example.com" --variable PAGE_LINK_DOMAIN="example.page.link"

Use variable `APP_DOMAIN` specify web URL where your app will start an activity to handle the link.

Use variable `PAGE_LINK_DOMAIN` specify your `*.page.link` domain.

Use variable `FIREBASE_DYNAMIC_LINKS_VERSION` and `FIREBASE_CORE_VERSION` to override dependency version on Android.

## Notification Action Buttons
This plugin version allows you to send action buttons on you notification on both Android and iOS. 

To do so you have to perfom a little change on JSON sent from FCM. This JSON has to be different on Android and iOS.

**NOTE:** In Android you have access to inline reply, in iOS you don't have access to it in this version.


### Android notification JSON

```
{
  "registration_ids": ["device id"],
  "data": {
    "title": "AUX Scrum",
    "text": "Scrum: Daily touchbase @ 10am Please be on time so we can cover everything on the agenda.",
    "actions": [
      {
        "icon": "emailGuests",
        "title": "EMAIL GUESTS",
        "callback": "emailGuests",
      },
      {
        "icon": "snooze",
        "title": "SNOOZE",
        "callback": "snooze"
      }
    ]
  }
}
```
**NOTE:** Do not add `notification: { title: "", body: "" }` on your notification JSON or the action buttons will not appear on notification.

### iOS notification JSON

```
"notification": {
        "title": "Notification Title",
        "body": "Notification message",
		"click_action": "invite"
}
```

### How to deal with clicked Actions

When the user clicks on an action in the `Firebase.onNotificationOpen()` you will receive an access to a JSON where there will be an `actionCallback` key where the value is the action callback/click_action that you specified on your JSON. On Android there will be an extra key named `inlineReply` with the user reply to that notification. 

### IMPORTANT FOR IOS
On iOS, at the starting of the app, you have to pass an JSON with the possible action buttons on the notifications. To do so you have to use the method: `Firebase.init(JSON)`

**JSON example:** 
```
{
    "ios": {
        categories: {
      invite: {
        yes: {
          callback: 'accept',
          title: 'Accept',
          foreground: true,
          destructive: false
        },
        no: {
          callback: 'reject',
          title: 'Reject',
          foreground: true,
          destructive: false
        },
        maybe: {
          callback: 'maybe',
          title: 'Maybe',
          foreground: true,
          destructive: false
        }
      },
      delete: {
        yes: {
          callback: 'doDelete',
          title: 'Delete',
          foreground: true,
          destructive: true
        },
        no: {
          callback: 'cancel',
          title: 'Cancel',
          foreground: true,
          destructive: false
        }
      }
    }
}
```