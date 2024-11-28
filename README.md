# bLinkup SDK docs

Getting started and best practices of using the bLinkup SDK.

The bLinkupSDK will enable users to achieve these three goals

1. Connect with friends on the app
2. Notify users when their friends are at the event (using smart tracking & proximity)
3. Easily send invites to locations on a venue map

To achieve these functions the following steps need to be taken:

- Sign-up, using their name, username, and phone number
- Login, via a texted authentication code
- Find friends, by searching name or matching contacts
- Send and accept friend requests
- Get a list of friends and notify users about which friends are at an event
- Send invites to venue specific points of interest

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

### Implement SDK Dependency

Swift:

add the dependency using Swift Package Manager '[https://github.com/blinkupsdk/bLinkupSwiftSDK](https://github.com/blinkupsdk/bLinkupSwiftSDK)'

```swift
import bLinkup
```

Gradle:

Place the following line in the dependency block of your applications build.gradle file

```kotlin
implementation 'com.github.blinkupsdk:bLinkupAndroidSDK:2.3.1'
```

## Out-of-the-box UI

The bLinkup SDK provides a complete user interface which can perform all of the functions listed above and requires only a couple lines of code.

- SwiftUi

```swift
BlinkupRootScreen(customer: .init(id: "<token>", name: "optional name"),
                  branding: .init(primary: UIColor(red: 0, green: 0.25, blue: 0.125, alpha: 1),
                                  secondary: UIColor(red: 0.8, green: 0.05, blue: 0.2, alpha: 1),
                                  fontName: "HelveticaNeue",
                                  logo: UIImage(named: "Logo2"),
                                  name: c.name),
                  autoClose: true,
                  onClose: { self.showBlinkup = false })
```

- UIKit

```swift
let vc = BlinkupRootViewController(customer: .init(id: "<token>", name: "optional name"),
                                   branding: .init(primary: UIColor(red: 0, green: 0.25, blue: 0.125, alpha: 1),
                                                   secondary: UIColor(red: 0.8, green: 0.05, blue: 0.2, alpha: 1),
                                                   fontName: "HelveticaNeue",
                                                   logo: "Optional Customer Logo Image Name",
                                                   name: "Optional Customer Name",
                                                   title: "Optional Title"),
                                   onClose: { [weak self] in self?.hideBlinkup() })
self.present(vc, animated: true)
```

- Kotlin

Optionally, define a theme:

```xml
    <style name="YourTheme" parent="DefaultTheme">
        <item name="primaryColor">@color/yourPrimaryColor</item>
        <item name="secondaryColor">@color/yourSecondaryColor</item>
    </style>
```

Start the SDK UI with the call:

```kotlin
BlinkupUISDK.launch(
  context,
  "your client ID",
  "Your Brand name",
  "Custom Actionbar Title"
  R.style.YourTheme,
  R.drawable.yourLogo
)
```

Where 3 last parameters are optional. You may proceed with the default theme, actionbar title("Connect") and without the logo:

```kotlin
BlinkupUISDK.launch(
  context,
  "your client ID",
  "Your Brand name",
)
```

### Set Push ID
To set the push ID (to receive it as one of the parameters the webhook would be sending to defined endpoint):
```swift
bLinkup.setPushID(pushId, completion: { _ in })
```
```kotlin
BlinkupUISDK.setPushId(pushId)
```
Using this value, you need to identify which exact instance of your application the push notification needs to be sent. 
For example, it could be your internal user ID. Then once you get the request from the webhook, using this user ID you would identify push token (or other data) that you require to send the push message to appropriate user.

The easiest way of implementing this would be to pass the push token itself as push ID. That way you won't need to identify which user needs the push message, and can just use this push token to send push messages directly.
Here is an example of setting the push ID the moment the application provides you with the push token.

```swift
func application(_ application: UIApplication,
                    didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data
) {
    let token = deviceToken.map({ String(format: "%02.2hhx", $0) }).joined()
    bLinkup.setPushID(token, completion: { _ in })
}
```

```kotlin
FirebaseMessaging.getInstance().token.addOnCompleteListener { task ->
    if (!task.isSuccessful) {
        return@addOnCompleteListener
    }

    // Get new FCM registration token
    val token = task.result
    BlinkupUISDK.setPushId(token)
}
```

This UI will handle all of the bLinkup capabilities, no further work is required and you are all done! 🥳

If you want to integrate bLinkup SDK further into your app then continue reading.

### Metadata
Metadata can be set for the BlinkUp user by the parent app. It then will be sent via webhook along with the  notification data for the client service to determine which user and which devices they want to send the push message to.

Swift:
```swift
//to set metadata
bLinkup.setMetadata("value", forKey: "key")
//to delete metadata
bLinkup.removeMetadata("key")
```

Kotlin:
```kotlin
//to set metadata
BlinkupUISDK.setMetadata("key", "value")
//to delete metadata
BlinkupUISDK.deleteMetadata("key")
//to clear all metadata
BlinkupUISDK.deleteAllMetadata()
```


## Notifications 

The bLinkup SDK relies on your existing push notification ecosystem for sending notifications to your users.
If you don't set up push notifications, then your users will have to manually open the Blinkup SDK UI to check who is at an event with them, or to check for friend requests. 
Push notifications are enabled by providing us with a webhoook URL where your servers can receive information about the various notification events emitted by the API. 
Webhooks are delivered as POST requests made to URLs of your choosing with JSON data describing the event. 
There are two categories of webhook notifrications you may receive: Connections and Presence Alerts

### Connections 

*Connection Requests*

This notification happens when one user sends a connection request to another. 
Your server will receive a webhook with the following JSON body, where `source_user` is the user who sent the request, and `target_user` is the user who is receiving the request:

```json
{
  "type": "connection_request",
  "request": {
    "id": "UUIDv4",
    "status": "pending",
    "source_user": {
      "id": "UUIDv4",
      "name": "Sender's Name",
      "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
    },
    "target_user": {
      "id": "UUIDv4",
      "name": "Receiver's Name",
      "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
    }
  }
}
```

*Connection*

This notification happens when a user who received a connection request replies to that request, either by accepting or declining the request.
Your server will receive a webhook with the following JSON body:

```json
{
  "type": "connection",
  "connection": {
    "id": "UUIDv4",
    "status": "connected",
    "source_user": {
      "id": "UUIDv4",
      "name": "Sender's Name",
      "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
    },
    "target_user": {
      "id": "UUIDv4",
      "name": "Receiver's Name",
      "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
    }
}
```
The `status` field may have the values: `connected`, `rejected`, or `blocked`

### Presence

Your server will receive presence webhook payloads when a user either enters or leaves a location. 
The API sends one webhook per user connection, so if a user arrives at a location that already has 4 connections present, the API will generate 4 distinct webhook payloads (one per connection at that event).
The presence payload is as follows, with the `is_present` field denoting if the user is arriving or leaving the location: 

```json
{
  "type": "presence",
  "is_present": true,
  "inserted_at": "2025-01-01T00:00:00.0000Z",
  "about_user": {
    "id": "UUIDv4",
    "name": "Person's Name",
    "email": "person@email.com",
    "phone_number": "+1555555555",
    "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
  },
  "send_to_user": {
    "id": "UUIDv4",
    "name": "Receiver's Name",
    "email": "receiver@email.com",
    "phone_number": "+1555555555",
    "metadata": [
        {
          "key": "push_id",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        },
      ]
  },
  "place": {
    "id": "UUIDv4",
    "name": "Name of the place"
  }
}
```
