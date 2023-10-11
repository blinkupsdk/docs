# docs

Getting started and best practices of using the bLinkup SDK.

Use the bLinkupSDK to do these three main things

1. Connect with friends on the app
2. Notify friends when they're at the event (using smart tracking & proximity)
3. Easily send invites to locations on a venue map

To achieve these functions the following steps need to be taken:

- Sign-up, using name, username, phone number
- Login, via a texted code
- Find friends, by searching name or matching contacts
- Send and accept friend requests
- Get a list of friends and notify users about which friends are at an event
- Send invites to venue specific points of interest

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

## Initialization

When your app starts initialize bLinkup with your other packages. This should only be done once at app launch.

Swift:

```swift
let bLinkup = bLinkupSDK(clientId: "YOUR_API_KEY_HERE")
```

Kotlin

```kotlin
bLinkup.Init("YOUR_API_KEY_HERE", this)
```

## User Account Creation

To sign up a new user on bLinkup only three fields are required.

1. Username
2. Name
3. Phone Number

To validate if a user name is available use the following API:

### Check Username Availability

Swift:

```swift
bLinkup.usernameTaken(username: username): Boolean
```

Kotlin

```kotlin
var userNameTaken = userNameTaken(editText: EditText, applicationContext: Context)
userNameTaken.callUsernameAvailabilityAPI(username: String): Boolean

```

### Account Registration

Instead of passwords, users will authenticate via a text sent to their phone numbers.

When signing up, the first step will be calling `register` to claim a phone number.

Swift:

```swift
bLinkup.register(phoneNumber: String)
```

Kotlin

```kotlin
Blinkup.requestCode(phoneNumber: String)
Blinkup.confirmCode(verificationCode: String)

```

### User Login

To login there are two functions which need to be called.

1. Send the code to the user's phone number
2. Verify the code matches

Swift:

```swift
bLinkup.sessionCreate(phoneNumber: phoneNumber)
bLinkup.sessionValidate(phoneNumber: phoneNumber, code: code)
```

Kotlin

```kotlin
Blinkup.requestCode(phoneNumber: String)
Blinkup.confirmCode(verificationCode: String)
```

### Update User Profile

Swift:

```swift
bLinkup.userData(firstName: String, lastName: String, username: String): User
```

Kotlin

```kotlin
Blinkup.updateUser(name: String, email: String)
```

## Core presence loop

### Is at event

Swift:

```swift
await bLinkup.isAtEvent(isAtEvent: bool)
```

Kotlin

```kotlin
Blinkup.setUserAtEvent(isPresent: Boolean, place: Place)
```

## Connection Management

### Get current list of friends

Swift:

```swift
try await bLinkup.friendList()
```

Kotlin

```kotlin
Blinkup.getFriendList()
```

### Friends at event

The core value of bLinkup is getting a list of a user's friends who are at a particular event.

Swift:

```swift

```

Kotlin

```kotlin
Blinkup.getUsersAtEvent(place: Place)
```

### Friend Search

Find other bLinkup users (scoped by API key) by searching. The passed string will look for any matches in username or name.

Swift:

```swift
try await bLinkup.usernameSearch(searchTerm: searchTerm)
```

Kotlin

```kotlin
Blinkup.findUsers(query: String)
```

### Contact Search

Finding users who are also in your contacts uses the platform contacts API to get the users contacts and send a list of phone numbers to the backend looking for matches. This is the easiest way to find all possible users without needing to search for each user.

Swift:

```swift
try await blinkup.extractPhoneContacts(): Array<Contact>
```

Kotlin

```kotlin
Blinkup.findContacts()

```

### Send Connect request to User

Swift:

```swift
bLinkup.friendRequest(friendRequestId: friendRequestId)
```

Kotlin

```kotlin
Blinkup.sendFriendRequest(request: ConnectionRequest)
```

### Get list of Connection Request

Swift:

```swift

```

Kotlin

```kotlin
Blinkup.getFriendRequests()
```

### Accept and deny request

Swift:

```swift
bLinkup.acceptFriend(acceptFriendRequestId: acceptFriendRequestId)
bLinkup.denyFriend(denyFriendRequestId: denyFriendRequestId)
```

Kotlin

```kotlin
Blinkup.acceptFriendRequest(request: ConnectionRequest)
Blinkup.denyFriendRequest(request: ConnectionRequest)

```

## bLinkpoints

bLinkpoints are points of interest at an event. These are set by you and are consumed in to the app by a JSON file which describes the point, positions it on a map image, and includes a URL to an image which can be shared when a user taps to send the bLinkpoint to a friend.

### Get list of Events

Swift:

```swift

```

Kotlin

```kotlin
Blinkup.getEvents()

```



### Displaying the map

Displaying the map and putting the interactive points on the app is a UI element provided by bLinkup. Call the following API to display a modal  which the user can interact with to dismiss or send invites to their friends.

### Sending meet ups
