# docs

Getting started and best practices of using the bLinkup SDK.

## Getting an API Key

email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com)

## Initialization

Swift:

```swift
bLinkup.Init("YOUR_API_KEY_HERE")
```

Kotlin

```kotlin
bLinkup.Init("YOUR_API_KEY_HERE")
```

## User Account Creation

### Check Username Availability

Swift:

```swift
bLinkup.usernameTaken(username: username)
```

Kotlin

```kotlin

```

### SMS Verification

Swift:

```swift

```

Kotlin

```kotlin

```

### Create user

Swift:

```swift
bLinkup.userData(firstName: firstName, lastName: lastName, username: username)
```

Kotlin

```kotlin

```

## Core location loop

## Connection Management

### Get current list of friends

Swift:

```swift
try await bLinkup.friendList()
```

Kotlin

```kotlin

```

### Friend Search

Swift:

```swift
try await bLinkup.usernameSearch(searchTerm: searchTerm)
```

Kotlin

```kotlin

```

### Contact Search

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

### Displaying the map

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
