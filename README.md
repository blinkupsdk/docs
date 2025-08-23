# bLinkup SDK docs

Getting started and best practices of using the bLinkup SDK.

The bLinkupSDK will enable users to achieve these three goals

1. **Connect with friends on the app**
2. **Notify users when their friends are at the same event or venue** (using smart tracking & proximity)
3. **Easily send invites to specific points of interest on a venue map**
4. **Extend meetups beyond the venue with the Bar Network** – invite friends to partner bars, access bar-specific locations, and unlock deals or rewards

To achieve these functions, the following steps are taken:
- **Sign-up:** Users enter their name, username, and phone number
- **Login:** Via a texted authentication code
- **Find friends:** Search by name or match from contacts
- **Send and accept friend requests**
- **Get a list of friends** and see which friends are at an event
- **Send invites:**
    - To **venue-specific points of interest** (on the map inside the event)
    - To **bar network locations**, where users can meet outside the venue, get notified when friends arrive, and redeem offers or promotions

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

### Implement SDK Dependency

Swift:

Add the dependency using Swift Package Manager '[Package](https://github.com/blinkupsdk/bLinkupSwiftSDK)'.
Implement SDK initialization on every app start(application(_:didFinishLaunchingWithOptions:), YourApp's init, ..).
```swift
import bLinkup
```
```swift
bLinkup.configure()
```
Gradle:

Add Jitpack repository to the list of your repositories:

```kotlin
repositories {
    ...
    maven { url "https://jitpack.io" }
}
```

Place the following line in the dependency block of your applications build.gradle file

```kotlin
implementation 'com.github.blinkupsdk:bLinkupAndroidSDK:3.0.0'
```

For the geodence to trigger and check the users in for the events, add the following broadcast receiver into your app's manifest file:
```xml
    <receiver
        android:name="com.blinkupapp.sdk.data.geofence.GeofenceBroadcastReceiver"
        android:exported="true"
        android:enabled="true"/>
```

And the following permissions:

```xml
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

If you are using Proguard or R8 (buildType flag minifyEnabled true), then you need to keep model classes used for serialization/deserialization

```groovy
# Keep model classes used for serialization/deserialization
-keep class com.blinkupapp.sdk.data.model.** { *; }
```

Additionally, if you are using R8 full mode, you would need to add the following

```groovy
#If you are using R8 full mode, then you need the following rules to keep the signatures of the classes that are being used by Retrofit.
# Keep generic signature of Call, Response (R8 full mode strips signatures from non-kept items).
 -keep,allowobfuscation,allowshrinking interface retrofit2.Call
 -keep,allowobfuscation,allowshrinking class retrofit2.Response

# With R8 full mode generic signatures are stripped for classes that are not
# kept. Suspend functions are wrapped in continuations where the type argument
# is used.
-keep,allowobfuscation,allowshrinking class kotlin.coroutines.Continuation
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

Start the SDK UI with the call:

```kotlin
BlinkupUISDK.launch(
    context,
    "your client ID"
)
```
You can also launch BlinkupUISDK as a Fragment and place it inside your UI yourself:

```kotlin
BlinkupUISDK.createLaunchFragment("your client ID") {
    //OnExit function  onExit: () -> Unit
}

```
where ```onExit: () -> Unit``` - function that will be called when user presses "Exit" button in the BlinkupUISDK. 
Since with the Fragment we can't bring your app state to the one you had before launching the BlinkupUISDK with a fragment (like we do when launching it with Activity by just finishing the activity), you would need to remove the BlinkupUISDK fragment from your UI yourself.

### Metadata

Metadata can be set for the BlinkUp user by the parent app. It then will be sent via webhook along with the notification data for the client service to determine which user and which devices they want to send the push message to.

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
If you don't set up push notifications, then your users will have to manually open the bLinkup SDK UI to check who is at an event with them, or to check for friend requests.
Push notifications are enabled by providing us with a webhoook URL where your servers will receive information about the various notification events emitted by the API.
Webhooks are delivered as POST requests with JSON data describing the event. There are three categories of webhook notifications each with two types.

1. Connections, relating to friend requests

   - `connection_request` when a user gets sent a connection request
   - `connection` when the connection request is accepted or rejected

2. Presence, relating to being at the same Place as a friend

   - `presence` when a user is at a place and a new friend arrives
   - `connections_presence` when a user enters a place and one or more friends are there

3. Events, relating to bar network events and deals

   - `invite` when a user gets sent an invite
   - `webhook_redeemable` when a user's welcome redeemable becomes available (i.e. event starts)

### Connection types

#### connection_request

This webhook is sent when one user sends a connection request to another.
Your server receives a webhook with the following JSON body, where `source_user` is the user who sent the request, and `target_user` is the user who receives the request:

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
        }
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
        }
      ]
    }
  }
}
```

#### connection

This notification happens when a user replies to a friend request, either by accepting or declining the request.
Your server receives a webhook with the following JSON body:

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
        }
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
        }
      ]
    }
  }
}
```

The `status` field may have the values: `connected`, `rejected`, or `blocked`

### Presence types

Your server receives presence webhook payloads when a user enters a Place where one more more of their friends are currently Present.
The API sends one webhook per user connection, so if a user arrives at a location that already has 4 friends present, the API will generate 4 distinct webhook payloads (one per connection at that event). There are two types of presence webhook payloads. `presence` and `connections_presence`. The `presence` is sent when a user is present and a friend arrives. While `connections_presence` is sent when a user arrives to a Place.
The difference between these is because when a user arrives to a place they will receive a list of friends present and not a unique notification for each friend present. While the friends who are present each receive a notification their friend has arrived.

#### presence

```json
{
  "about_user": {
    "email_address": "username",
    "id": "UUIDv4",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      },
      {
        "key": "custom_key",
        "value": "..."
      }
    ],
    "name": "username",
    "phone_number": "+1555555555"
  },
  "inserted_at": "2025-01-01T00:00:00.0000Z",
  "is_present": true,
  "place": {
    "id": "UUIDv4",
    "name": "Place name"
  },
  "send_to_user": {
    "email_address": "username",
    "id": "UUIDv4",
    "metadata": [
      {
        "key": "custom_key",
        "value": "..."
      }
    ],
    "name": "user's name",
    "phone_number": "+1555555555"
  },
  "type": "presence"
}
```

#### connections_presence

```json
{
  "friends_names": [
    "friend name 1",
    "friend name 2"
  ],
  "inserted_at": "2025-01-01T00:00:00.0000Z",
  "is_present": true,
  "place": {
    "id": "UUIDv4",
    "name": "Place name"
  },
  "send_to_user": {
    "email_address": "username",
    "id": "UUIDv4",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      },
      {
        "key": "the_metadata_key",
        "value": "the_metadata_value"
      }
    ],
    "name": "username",
    "phone_number": "+1555555555"
  },
  "type": "connections_presence"
}
```

### Event types

#### invite

This webhook is sent when one user sends an invite to another.
Your server receives a webhook with the following JSON body, where `source_user` is the user who sent the request, and `target_user` is the user who receives the request:

```json
{
  "type": "invite",
  "invite": {
    "id": "443a15b1-4af0-488a-941b-ca5ea437b6d9",
    "source_user": {
      "email_address": "username",
      "id": "76600912-6beb-4687-9eb9-d6af3fbf995a",
      "metadata": [
        {
          "key": "push_token",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        }
      ],
      "name": "Jacky Runte III",
      "phone_number": "+16085673555"
    },
    "spot": {
      "address": "199 Keeling Courts Apt. 764, New York",
      "id": "40bfe13d-1f93-4ad9-958c-c1a73919d3bf",
      "latitude": 64.84873162337371,
      "longitude": -158.59206468706833,
      "name": "Bogan, Kozey and Bernier",
      "promotion": "20% off beer",
      "type": "bar"
    },
    "target_user": {
      "email_address": "username",
      "id": "76600912-6beb-4687-9eb9-d6af3fbf995a",
      "metadata": [
        {
          "key": "push_token",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        }
      ],
      "name": "Jacky Runte III",
      "phone_number": "+16085673555"
    }
  }
}
```

#### welcome_redeemable

This webhook is sent when user's welcome redeemable becomes available on event start.
Your server receives a webhook with the following JSON body:

```json
{
  "type": "welcome_redeemable",
  "redeemable": {
    "id": "9ac09263-069e-4d12-84bf-ac4d8ab7bb4e",
    "spot": {
      "address": "199 Keeling Courts Apt. 764, New York",
      "id": "40bfe13d-1f93-4ad9-958c-c1a73919d3bf",
      "latitude": 64.84873162337371,
      "longitude": -158.59206468706833,
      "name": "Bogan, Kozey and Bernier",
      "promotion": "20% off beer",
      "type": "bar"
    },
    "spot_id": "20ad31c3-4165-4a75-86b8-f8462fb3794a",
    "status": "available",
    "type": "incoming_invite",
    "user": {
      "email_address": "username",
      "id": "76600912-6beb-4687-9eb9-d6af3fbf995a",
      "metadata": [
        {
          "key": "push_token",
          "value": "..."
        },
        {
          "key": "custom_key",
          "value": "..."
        }
      ],
      "name": "Jacky Runte III",
      "phone_number": "+16085673555"
    }
  }
}
```
