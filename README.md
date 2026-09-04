# BlinkUp SDK docs

Getting started and best practices of using the BlinkUp SDK.

The BlinkUp SDK will enable users to achieve these goals

1. **Earn Attendance Rewards**
2. **Redeem Deals**
3. **Enter Prize Draws**

To achieve these functions, the following steps are taken:
- **Sign-up:** Users enter their name, username, and phone number
- **Login:** Via a texted authentication code
- **Check-in:** At the stadium, at a partner bar, or from home by hosting or joining a **Home Watch Party**
- Checked-in users are then **automatically entered** to participate in the drawings

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

### Implement SDK Dependency

Swift:

Add the dependency using Swift Package Manager '[Package](https://github.com/blinkupsdk/bLinkupSwiftPackage)'.
Implement SDK initialization on every app start(application(_:didFinishLaunchingWithOptions:), YourApp's init, ..).
```swift
import bLinkupSDK
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
implementation 'com.github.blinkupsdk:bLinkupAndroidSDK:4.1.0'
```

Add the following permissions to your app's manifest file:

```xml
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

Location is used in the foreground only — to detect the venue the user is at
and to verify check-ins. The SDK does **not** use background location or
geofencing, so no background location permission and no broadcast receiver are
required.

> Upgrading from 3.x: remove the
> `com.blinkupapp.sdk.data.geofence.GeofenceBroadcastReceiver` receiver from
> your manifest (the class no longer exists) and drop
> `ACCESS_BACKGROUND_LOCATION` if you only requested it for BlinkUp.
> Users are no longer checked in/out automatically — they check in explicitly
> in the SDK UI (or via `Blinkup.setUserAtEvent`).

#### Home Watch Party (Bluetooth)

Since 4.1.0 fans can watch from home together: one fan hosts a Home Watch
Party and nearby fans find and join it over Bluetooth, which checks everyone
in the party in — the same as being at a bar. The feature is enabled per
integration by your BlinkUp contact.

The Bluetooth permissions (`BLUETOOTH_SCAN` with `neverForLocation`,
`BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT`) are declared in the SDK's own
manifest and merge into your app — nothing to add. No location permission is
involved: the SDK asks the user for the "Nearby devices" permission when the
section is first shown. The feature is available on Android 12 and newer;
older devices simply don't see the section.

> If your app already declares `BLUETOOTH_SCAN` itself, declare it the same
> way — with `android:usesPermissionFlags="neverForLocation"` — or the
> manifest merger will report an attribute conflict at build time. The flag is
> an app-wide statement that no Bluetooth scan in the app is used to infer
> location; if your app does use BLE scanning for location, talk to your
> BlinkUp contact before integrating Home Watch Party.

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

The BlinkUp SDK provides a complete user interface which can perform all of the functions listed above and requires only a couple lines of code.

- SwiftUi

```swift
.sheet(isPresented: $showBlinkup) {
  BlinkupRootScreen(token: "<token>")
}
```
or
```swift
.fullScreenCover(isPresented: $showBlinkup) {
  BlinkupRootScreen(token: "<token>", autoClose: true)
}
```

- UIKit

```swift
let rootView = BlinkupRootScreen(token: "<token>")
present(UIHostingController(rootView: rootView), animated: true)
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

## Using Google Map
On Android, for the Bar Network to show the map it is required to have the Google Maps API key.
You need to add it to your manifest like this:

```xml
    <meta-data android:name="com.google.android.geo.API_KEY"
                android:value="${MAPS_API_KEY}" />
```

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

### PushId

In the versions 2.x there wes a separate method BlinkupUISDK.setPushId("your_push_id"), which under the hood was just calling BlinkupUISDK.setMetadata("push_id", "your_push_id").
In version 3.0.4 it has been re-intruduced for backwards-compatibility

### External ID

Since 4.1.0 your app can give BlinkUp its own identifier for the user, so
that every webhook can be matched to the right account in your system without
searching the metadata list. Any string works.

Kotlin:

```kotlin
//to set your own user id
BlinkupUISDK.setExternalId("your_user_id")
//to forget it, e.g. when your app's user changes
BlinkupUISDK.clearExternalId()
```

Like `setPushId`, it may be called before the user has signed in to BlinkUp;
the value is sent once they are. Setting it again replaces the previous value,
and it survives a BlinkUp logout, since it belongs to your user rather than to
the BlinkUp session.

Under the hood it is stored as metadata under the key `external_id`, and every
webhook payload exposes it as a top-level `user.external_id` field (`null` when
never set) — see the Notifications section.

## Notifications

The BlinkUp SDK relies on your existing push notification ecosystem for sending notifications to your users.
If you don't set up push notifications, your users will have to open the BlinkUp SDK UI themselves to find out about prizes they have won.
Push notifications are enabled by providing us with a webhook URL where your servers will receive information about the various notification events emitted by the API.
Webhooks are delivered as POST requests with JSON data describing the event. The main types are:

- `checkin` — a fan checks in for the first time in an event (stadium, bar or Home Watch Party)
- `draw_winner` — a fan wins an activation (e.g. Fan of the Game)
- `season_rewards_winner` — a fan wins the Season Rewards
- `watchparty_group_winner` — the fan's Home Watch Party wins a draw
- `announcement` — a message from the venue to fans, e.g. to everyone checked in at the Bar of the Game

Each type is switched on per integration; ask your BlinkUp contact to enable
the ones you handle. The complete, always up-to-date list — with the exact
payload schema of every webhook enabled for *your* integration — is available
as an OpenAPI/Swagger document; ask your BlinkUp contact for your integration's
documentation link.

Every payload carries the target `user`, including the user's `metadata` (see the Metadata section above) and, since 4.1.0, `external_id` — the identifier your app set with `setExternalId`, or `null` — use either to route the push to the right device.

### checkin

Sent once per fan per event, on their first check-in of any kind. Later
check-ins during the same event — moving to another bar, or checking out and
back in — are not repeated, and nothing is sent when no event is on.
`checkin_type` is `place` (the stadium), `spot` (a partner bar) or
`home_watchparty`; `method` is `manual` when the fan tapped to check in and
`location` when the SDK placed them there. Exactly one of `place`, `spot` or
`watchparty_group` is present, matching the type.

```json
{
  "type": "checkin",
  "checkin_type": "spot",
  "method": "manual",
  "inserted_at": "2026-09-13T20:31:04Z",
  "event": {
    "id": "UUIDv4",
    "name": "Week 1 at Eagles"
  },
  "spot": {
    "id": "UUIDv4",
    "name": "Scotty's Sports Bar"
  },
  "user": {
    "id": "UUIDv4",
    "name": "Fan's Name",
    "phone_number": "+1555555555",
    "email_address": "username",
    "external_id": "your_user_id",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      }
    ]
  }
}
```

For a Home Watch Party check-in, `watchparty_group` replaces `spot`:

```json
  "checkin_type": "home_watchparty",
  "watchparty_group": {
    "id": "UUIDv4",
    "name": "Mike's living room",
    "is_host": true
  }
```

### draw_winner

Sent when a prize draw is triggered and the fan is selected as a winner, so you can notify them right away (e.g. "You won!"). `prize_name` is `null` when the draw has no prize attached, and `winner_rank` is `null` for unranked selection methods.

```json
{
  "type": "draw_winner",
  "activation_name": "Fan of the Game",
  "prize_name": "Bar Tab",
  "winner_rank": 1,
  "user": {
    "id": "UUIDv4",
    "name": "Winner's Name",
    "phone_number": "+1555555555",
    "email_address": "username",
    "external_id": "your_user_id",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      },
      {
        "key": "custom_key",
        "value": "..."
      }
    ]
  }
}
```

### watchparty_group_winner

Sent to **every member** of a Home Watch Party that wins a draw, host and
guests alike. The prize itself is held by the host, so tell members their
party won rather than that they hold the prize. Same fields as `draw_winner`
plus the party that won.

```json
{
  "type": "watchparty_group_winner",
  "activation_name": "Home Watch Party Draw",
  "prize_name": "Signed Jersey",
  "winner_rank": 1,
  "watchparty_group": {
    "id": "UUIDv4",
    "name": "Mike's living room"
  },
  "user": {
    "id": "UUIDv4",
    "name": "Member's Name",
    "phone_number": "+1555555555",
    "email_address": "username",
    "external_id": "your_user_id",
    "metadata": []
  }
}
```

### season_rewards_winner

Sent when the Season Rewards winners are drawn and the fan is among them. Same shape as `draw_winner`; `winner_rank` is the fan's leaderboard rank.

```json
{
  "type": "season_rewards_winner",
  "activation_name": "Season Rewards",
  "prize_name": "Season Tickets",
  "winner_rank": 3,
  "user": {
    "id": "UUIDv4",
    "name": "Winner's Name",
    "phone_number": "+1555555555",
    "email_address": "username",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      }
    ]
  }
}
```

### announcement

Sent when the venue broadcasts a message — to all fans, or only to the fans checked in at a specific bar or place (e.g. announcing the Bar of the Game). One webhook is delivered per recipient.

```json
{
  "type": "announcement",
  "message": "Tonight's Bar of the Game is Scotty's Sports Bar — you're in the right place!",
  "user": {
    "id": "UUIDv4",
    "name": "Fan's Name",
    "phone_number": "+1555555555",
    "email_address": "username",
    "metadata": [
      {
        "key": "push_token",
        "value": "..."
      }
    ]
  }
}
```
