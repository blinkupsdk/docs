# docs

Getting started and best practices of using the bLinkup SDK.

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

## Initialization

When your app starts initialize bLinkup with your other package. This should only be done once at app launch.

Swift:

```swift
bLinkup.Init("YOUR_API_KEY_HERE")
```

Kotlin

```kotlin
bLinkup.Init("YOUR_API_KEY_HERE")
```

## User Account Creation

To sign up a new user on bLinkup only three fields are required.

1. Username
2. Name
3. Phone Number

To validate if a user name is avaiable use the following API:

### Check Username Availability

Swift:

```swift
bLinkup.usernameTaken(username: username)
```

Kotlin

```kotlin

```

### SMS Verification

Instead of passwords users will authenticate via a text sent to their phone numbers.

Swift:

```swift

```

Kotlin

```kotlin

```

### Create user

Swift:

```swift
bLinkup.userData(firstName: string, lastName: string, username: string)
```

Kotlin

```kotlin

```

## Core presence loop

### Is at event

Swift:

```swift
await bLinkup.isAtEvent(isAtEvent: bool)
```

Kotlin

```kotlin

```

## Connection Management

### Get current list of friends

Swift:

```swift
try await bLinkup.friendList()
```

Kotlin

```kotlin

```

### Friends at event

The core value of bLinkup is getting a list of a user's friends who are at a particular event.

Swift:

```swift

```

Kotlin

```kotlin

```

### Friend Search

Find other bLinkup users (scoped by API key) by searching. The passed string will look for any matches in username or name.

Swift:

```swift
try await bLinkup.usernameSearch(searchTerm: searchTerm)
```

Kotlin

```kotlin

```

### Contact Search

Finding users who are also in your contacts uses the platform contacts API to get the users contacts and send a list of phone numbers to the backend looking for matches. This is the easiest way to find all possible users without needing to search for each user.

Swift:

```swift
try await blinkup.extractPhoneContacts()
```

Kotlin

```kotlin

```

### Send Connect request to User

Swift:

```swift
bLinkup.friendRequest(friendRequestId: friendRequestId)
```

Kotlin

```kotlin

```

### Accept and deny request

Swift:

```swift
bLinkup.acceptFriend(acceptFriendRequestId: acceptFriendRequestId)
bLinkup.denyFriend(denyFriendRequestId: denyFriendRequestId)
```

Kotlin

```kotlin

```

### Block user

Swift:

```swift

```

Kotlin

```kotlin

```

## bLinkpoints

bLinkpoints are points of interest at an event. These are set by you and are consumed in to the app by a JSON file which describes the point, positions it on a map image, and includes a URL to an image which can be shared when a user taps to send the bLinkpoint to a friend.

### Displaying the map

Displaying the map and putting the interactive points on the app is a UI element provided by bLinkup. Call the following API to display a modal  which the user can interact with to dismiss or send invites to their friends.

Swift:

```swift

```

Kotlin

```kotlin

```

### Sending meet ups

Swift:

```swift

```

Kotlin

```kotlin

```
