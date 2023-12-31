

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

Java

```java
BlinkupWrapper.Init("YOUR_API_KEY_HERE", context: Context)
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

Java

```java
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
        Blinkup.requestCode(phoneNumber: String): String
    } catch (e: BlinkupException){
        return@launch
    }
}

//confirm phone number with the SMS code
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.confirmCode(verificationCode: String): User
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
//request SMS code for the phone number
BlinkupWrapper.requestCode(phone, new ResultListener<String>() {  
    @Override  
  public void onResult(String result) {  
          
    }  
  
    @Override  
  public void onError(@NonNull Exception exception) {  
        exception.printStackTrace();  
    }  
});

//confirm phone number with the SMS code
BlinkupWrapper.confirmCode("code", new ResultListener<User>() {  
    @Override  
  public void onResult(User result) {  
         
    }  
  
    @Override  
  public void onError(@NonNull Exception exception) {  
         
    }  
});
```

After this you need to check if you need to fill in the user profile and fill the details if need:

Swift:

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
Kotlin:

```kotlin
if(Blinkup.isUserDetailsRequired()) {
	GlobalScope.launch(Dispatchers.IO){
	    try {
	        Blinkup.updateUser(name: String, email: String): User
	    } catch (e: BlinkupException){
	        return@launch
	    }
	}
}
```

Java:

```java
if(BlinkupWrapper.isUserDetailsRequired()) {
	BlinkupWrapper.updateUser(name: String, email: String, new ResultListener<User>() {  
	    @Override  
		public void onResult(User result) {  
	         
		}  
	  
	    @Override  
	    public void onError(@NonNull Exception exception) {  
	         
	    }  
	});
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
        Blinkup.requestCode(phoneNumber: String): String
    } catch (e: BlinkupException){
        return@launch
    }
}

//confirm phone number with the SMS code
GlobalScope.launch(Dispatchers.IO) {
    try {
        Blinkup.confirmCode(verificationCode: String): User
    } catch (e: BlinkupException){
        return@launch
    }
}

```

Java:

```java
//request SMS code for the phone number
BlinkupWrapper.requestCode(phone, new ResultListener<String>() {  
    @Override  
  public void onResult(String result) {  
          
    }  
  
    @Override  
  public void onError(@NonNull Exception exception) {  
        exception.printStackTrace();  
    }  
});

//confirm phone number with the SMS code
BlinkupWrapper.confirmCode("code", new ResultListener<User>() {  
    @Override  
  public void onResult(User result) {  
         
    }  
  
    @Override  
  public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        Blinkup.updateUser(name: String, email: String): User
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
BlinkupWrapper.updateUser(name: String, email: String, new ResultListener<User>() {  
    @Override  
	public void onResult(User result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        Blinkup.checkSessionAndLogin(): User
    } catch (e: BlinkupException){
        return@launch
    }
}

```

Java:

```java

BlinkupWrapper.checkSessionAndLogin(new ResultListener<User>() {  
    @Override  
	public void onResult(User result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});


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
    val isAtEvent = Blinkup.isUserAtEvent(place: Place): Boolean
} catch(e: BlinkupException) {
    return@launch
}
```

Java:

```java
BlinkupWrapper.isUserAtEvent(place: Place, new ResultListener<Boolean>() {  
    @Override  
	public void onResult(Boolean result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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

Java:

```java
BlinkupWrapper.setUserAtEvent(isPresent: Boolean, place: Place, new ResultListener<Unit>() {  
    @Override  
	public void onResult(Unit result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        val friendsList = Blinkup.getFriendList(): List<Connection>
    } catch (e: BlinkupException){
        return@launch
    }
}

```

Java:

```java
BlinkupWrapper.getFriendList(new ResultListener<List<Connection>>() {  
    @Override  
	public void onResult(List<Connection> result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
```

### Friends at event

The core value of bLinkup is getting a list of a user's friends who are at a particular event.

Swift:

```swift
bLinkup.getFriendsAtPlace(Place, completion: { print($0) })
```

Kotlin:

```kotlin
        lifecycleScope.launch(Dispatchers.IO) {
            try {
                    val friendsAtEvent = Blinkup.getUsersAtEvent(placeId): List<Connection> 
                }

            } catch (e: BlinkupException) {
                Log.e("EventList", "failed to run getFriendsAtEventList", e)
                return@launch
            }
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
        Blinkup.findUsers(query: String): List<User>
    } catch (e: BlinkupException){
        return@launch
    }
}

```


Java:

```java
BlinkupWrapper.findUsers(query: String, new ResultListener<List<User>>() {  
    @Override  
	public void onResult(List<User> result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        Blinkup.findContacts(): List<Contact>
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
BlinkupWrapper.findContacts(new ResultListener<List<Contact>>() {  
    @Override  
	public void onResult(List<Contact> result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        Blinkup.sendFriendRequest(friend: User): Connection
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
BlinkupWrapper.sendFriendRequest(friend: User, new ResultListener<Connection>() {  
    @Override  
	public void onResult(Connection result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        val requests = Blinkup.getFriendRequests(): List<ConnectionRequest>
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
BlinkupWrapper.getFriendRequests(new ResultListener<List<ConnectionRequest>>() {  
    @Override  
	public void onResult(List<ConnectionRequest> result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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

Java:

```java
BlinkupWrapper.acceptFriendRequest(request: ConnectionRequest, new ResultListener<Unit>() {  
    @Override  
	public void onResult(Unit result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
BlinkupWrapper.denyFriendRequest(request: ConnectionRequest, new ResultListener<Unit>() {  
    @Override  
	public void onResult(Unit result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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
        val events = Blinkup.getEvents(): List<Place>
    } catch (e: BlinkupException){
        return@launch
    }
}
```

Java:

```java
BlinkupWrapper.getEvents(new ResultListener<List<Place>>() {  
    @Override  
	public void onResult(List<Place> result) {  
         
	}  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
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

Java:
Place `VenueMapView` in your layout (or create it with code).
Get it's instance, for example 
```kotlin
VenueMapView map = findViewById<VenueMapView>(R.id."your_map_id")
```
and set the event you want to show the mapo for into `VenueMapView`
```java
Place event = ...
map.setPlace(event)
```

### Accessing the BlinkPoints:
Swift:

Kotlin:
```kotlin
place.blinkpoints
```
Java:
```java
place.getBlinkpoints()
```



### Sending meet ups

Swift, Kotlin, Java:

This is handled by the map display function. When clicking on meetup spot on the map, it would offer to send a text message with invite to meet at that point.
