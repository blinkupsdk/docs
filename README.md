
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
bLinkup.configure(clientId: "YOUR_API_KEY_HERE")
```

Kotlin

```kotlin
Blinkup.Init("YOUR_API_KEY_HERE", context: Context)
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
Feature still in development
```

Kotlin

```kotlin
Feature still in development

```

### Account Registration

Instead of passwords, users will authenticate via a text sent to their phone numbers.

When signing up, the first step will be calling `register` to claim a phone number.

Swift:

```swift
bLinkup.requestCode(phone: String, completion: { [weak self] result in 
    switch result {
    case .failure(let error):
        print(error)
    case .success(let message):
        print(message)
        //self?.showConfirmation(phone)
    }
})
bLinkup.confirmCode(phone: String, code: String, completion: {  [weak self] in
    switch $0 {
    case .failure(let error):
        print(error)
    case .success(let user):
        print(user)
        //self?.showMainFunctionality(user)
    }
})
```

Kotlin:

```kotlin
//request SMS code for the phone number
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.requestCode(phoneNumber: String)
    } catch (e: BlinkupException){
        return@launch
    }
}

//confirm phone number with the SMS code
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.confirmCode(verificationCode: String)
    } catch (e: BlinkupException){
        return@launch
    }
}
```

After this you need to check if you need to fill in the user profile and fill the details if need:

```swift
if bLinkup.isUserDetailsRequired {
    bLinkup.updateUser(name: String, email: String, completion: { in 
        switch $0 {
        case .failure(let error):
            print(error)
        case .success(let user):
            print(user.id)
        }
    })
}
```

```kotlin
if(Blinkup.isUserDetailsRequired()) {
	GlobalScope.launch(Dispatchers.IO){
	    try {
	        Blinkup.updateUser(name: String, email: String)
	    } catch (e: BlinkupException){
	        return@launch
	    }
	}
}
```

### User Login

To login there are two functions which need to be called.

1. Send the code to the user's phone number
2. Verify the code matches

Swift:

```swift
bLinkup.requestCode(phone: String, completion: { print($0) })
bLinkup.confirmCode(phone: String, code: String, completion:{ print($0) })
```

Kotlin:

```kotlin
//request SMS code for the phone number
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.requestCode(phoneNumber: String)
    } catch (e: BlinkupException){
        return@launch
    }
}

//confirm phone number with the SMS code
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.confirmCode(verificationCode: String)
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Update User Profile

Swift:

```swift
bLinkup.updateUser(name: String, email: String, completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.updateUser(name: String, email: String)
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Check Current Session

Swift:

```swift
bLinkup.isLoginRequired 
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.checkSessionAndLogin()
    } catch (e: BlinkupException){
        return@launch
    }
}

```

## Core presence loop

### Is at event

Swift:

```swift
bLinkup.isUserAtEvent(Place, completion: { print($0) })
```

Kotlin:

```kotlin
try {
    val isAtEvent = Blinkup.isUserAtEvent(place: Place)
} catch(e: BlinkupException) {
    return@launch
}
```

### Set presence at event:

Swift:

```swift
bLinkup.setUserAtEvent(Bool, at: Place, completion: { print($0) })
```

Kotlin:
```kotlin
try {
    Blinkup.setUserAtEvent(isPresent: Boolean, place: Place)
} catch(e: BlinkupException) {
    return@launch
}
```

## Connection Management

### Get current list of friends

Swift:

```swift
bLinkup.getFriendList(completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        val friendsList = Blinkup.getFriendList()
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Friends at event

The core value of bLinkup is getting a list of a user's friends who are at a particular event.

Swift:

```swift
bLinkup.getFriendsAtPlace(Place, completion: { print($0) })
```

Kotlin:

```kotlin

```

### User Search

Find other bLinkup users (scoped by API key) by searching. The passed string will look for any matches in username or name.

Swift:

```swift
bLinkup.findUsers(query: String?, completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.findUsers(query: String)
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Contact Search

Finding users who are also in your contacts uses the platform contacts API to get the users contacts and send a list of phone numbers to the backend looking for matches. This is the easiest way to find all possible users without needing to search for each user.

Swift:

```swift
bLinkup.findContacts(completion: { result in })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.findContacts()
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Send Connect request to User

Swift:

```swift
bLinkup.sendConnectionRequest(user: User, completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.sendFriendRequest(friend: User)
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Get list of Connection Request

Swift:

```swift
bLinkup.getFriendRequests(completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        val requests = Blinkup.getFriendRequests()
    } catch (e: BlinkupException){
        return@launch
    }
}

```

### Accept and deny request

Swift:

```swift
bLinkup.acceptFriendRequest(ConnectionRequest, completion: { print($0) })
bLinkup.denyFriendRequest(ConnectionRequest,completion: { print($0) })
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        Blinkup.acceptFriendRequest(request: ConnectionRequest)
        Blinkup.denyFriendRequest(request: ConnectionRequest)
    } catch (e: BlinkupException){
        return@launch
    }
}

```

## bLinkpoints

bLinkpoints are points of interest at an event. These are set by you and are consumed in to the app by a JSON file which describes the point, positions it on a map image, and includes a URL to an image which can be shared when a user taps to send the bLinkpoint to a friend.

### Get list of Events

Swift:

```swift
bLinkup.getEvents(completion: {
    if let firstEvent = (try? $0.get())?.first {
        print(firstEvent.blinkpoints)
    }
})
```

Kotlin:

```kotlin
GlobalScope.launch(Dispatchers.IO){
    try {
        val events = Blinkup.getEvents()
    } catch (e: BlinkupException){
        return@launch
    }
}


```


### Displaying the map

Displaying the map and putting the interactive points on the app is a UI element provided by bLinkup. Call the following API to display a modal  which the user can interact with to dismiss or send invites to their friends.

Swift:

```swift
Feature still in development
let controller = bLinkup.VenueMapViewController(Place)
navigationController.push(controller, animated: true)
```

Kotlin:
Place `VenueMapView` in your layout (or create it with code).
Get it's instance, for example 
```kotlin
val map = findViewById<VenueMapView>(R.id."your_map_id")
```
and set the event you want to show the mapo for into `VenueMapView`
```kotlin
val event:Place = ...
map.place = event
```

### Sending meet ups

Swift, Kotlin:

This is handled by the map display function. When clicking on meetup spot on the map, it would offer to send a text message with invite to meet at that point.
