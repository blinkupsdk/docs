## Implement SDK Manually

Please reference the sample applications for examples of how to implement the bLinkup calls in your app.

- Swift
  - [GitHub bLinkup Swift Sample](https://github.com/blinkupsdk/bLinkupSwiftSample)
- Kotlin
  - [GitHub bLinkup Kotlin Sample](https://github.com/blinkupsdk/bLinkupKotlinSample)

### List of Data Classes

The following links provide a list of the different types of data classes and objects that are defined by the bLinkup SDK.

[Kotlin Data Classes](KotlinDataClasses.md)

### Initialization

When your app starts, initialize bLinkup with your other packages. This should only be done once at app launch.

Swift:

```swift
bLinkup.configure()
```

Kotlin:

```kotlin
Blinkup.init(context: Context)
```

Java:

```java
BlinkupWrapper.Init(context: Context)
```

### User Account Creation

To sign up a new user on bLinkup only two fields are required.

1. Name
2. Phone Number

#### Account Registration

Instead of passwords, users will authenticate via a text sent to their phone numbers.

When signing up, the first step will be to call the following, in order, to claim a phone number.

You also need to pass clientId as the parameter in this method

Swift:

```swift
bLinkup.requestCode(customer: Customer(id: "<<YOUR_API_KEY_HERE>>"), phoneNumber: String, completion: { [weak self] result in 
    switch result {
    case .failure(let error):
        print(error)
    case .success(let message):
        print(message)
        //self?.showConfirmation(phone)
    }
})
bLinkup.confirmCode(phoneNumber: String, verificationCode: String, completion: { [weak self] in
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
Blinkup.requestCode(clientId: String, phoneNumber: String)  
//returns a String
Blinkup.confirmCode(verificationCode: String) 
//returns a User
```

Java:

```java
//request SMS code for the phone number
BlinkupWrapper.requestCode(clientId, phone, new ResultListener<String>() {  
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

After this you need to check whether the user has a name and email. If needed, prompt the user to add those details:

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
Blinkup.isUserDetailsRequired() 
//returns a Boolean
Blinkup.updateUser(name: String, email: String) 
//returns updated User
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

#### User Login

To login there are two functions which need to be called.

1. Send the code to the user's phone number
2. Verify the code matches

Swift:

```swift
bLinkup.requestCode(phoneNumber: String, completion: @escaping (Result<String, Error>) -> Void)
bLinkup.confirmCode(phoneNumber: String, verificationCode: String, completion: @escaping (Result<User, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.requestCode(phoneNumber: String) 
//returns a String message, which you may use for debugging purpose or just ignore
Blinkup.confirmCode(verificationCode: String) 
//returns a logged in User

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

#### Check for a logged in user

Logged in users should not be required to login a second time. The following call will determine whether the current user has an active session.

Swift:

```Swift
bLinkup.isLoginRequired
```

Kotlin:

```Kotlin
Blinkup.isLoginRequired()
//returns a Boolean
```

Java:

```Java
BlinkupWrapper.isLoginRequired()
//returns a Boolean
```

#### Update User Profile

The user has the ability to modify their profile name and email. Utilizing the following call, the user and submit a string to change one or more of these fields.

Swift:

```swift
bLinkup.updateUser(name: String, email: String, completion: @escaping (Result<User, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.updateUser(name: String, email: String) 
//returns updated User
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

#### Check Current Session

If the current user's information is required, utilize the following call to return their User object.

Swift:

```swift
bLinkup.isLoginRequired 
```

Kotlin:

```kotlin
Blinkup.checkSessionAndLogin() 
//returns a User
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

#### Logout

In the event the user wishes to logout, the following call can be used remove the session tokens.

Swift:

```swift
bLinkup.logout(completion: @escaping (Error?) -> Void)
```

Kotlin:

```kotlin
Blinkup.logout()
```

Java

```java
BlinkupWrapper.logout();
```

## Core presence loop

The core value of bLinkup is getting a list of a user's friends who are at the same event as the user. Implyng Background Location is allowed, the SDK will use Geofences to automatically check users in/out of the events. 

#### Getting notified about automatic check in/out

```kotlin
Blinkup.setPresenceChangedListener { place, isPresent ->
    //react to updated user state. place: Place, isPresent: Boolean
}
```

Remove the listener:

```kotlin
Blinkup.removePresenceChangedListener()
```

### Is at event

In order to see who is at the same event as the user, the user must first check in to an event. The following call checks to see if the user is at a particular event and returns a boolean value.

Swift:

```swift
bLinkup.isUserAtEvent(_ place: Place, completion: @escaping @escaping (Result<Bool, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.isUserAtEvent(place: Place) 
//returns a Boolean
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

#### Set presence at event

This call will allow the user to set their presence at an event to true.

Swift:

```swift
bLinkup.setUserAtEvent(_ presence: Bool, at place: Place, completion: @escaping (Result<Presence, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.setUserAtEvent(isPresent: Boolean, place: Place)
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

Another version of this method includes  `PresenceConfidence` as 3rd parameter. It is used to determone how confident are we in the presence change event. Example: If the background location is enabled and if the device is not in power save mode - the confidence is `STRONG`, othervise `WEEK`. This is determined automatically in `Blinkup.setUserAtEvent(isPresent: Boolean, place: Place)` method. 3rd possible value of `PresenceConfidence` is `MANUAL` and should be used when user manually sets their presence

#### Friends at event

As a core value of bLinkup, the following call will return a list of Presence objects for friends at a given event.

Swift:

```swift
bLinkup.getFriendsAtPlace(_ place: Place, completion: @escaping (Result<[Presence], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.getUsersAtEvent(place: Place) 
//returns a List<Presence>
```

Java:

```java
BlinkupWrapper.getUsersAtEvent(place: Place, new ResultListener<List<User>>() {  
    @Override  
 public void onResult(List<User> result) {  
         
 }  
  
    @Override  
    public void onError(@NonNull Exception exception) {  
         
    }  
});
```

### Connection Management

#### Get current list of friends

Being able to see a list of existing friends is paramount to bLinkup. Utilizing the following call will return a list of existing connections.

Swift:

```swift
bLinkup.getFriendList(completion: @escaping (Result<[Connection], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.getFriendList() 
//returns a List<Connection>
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
#### Get friends present at same event as you

```kotlin
Blinkup.getFriendsAtMyEvents() 
//returns a List<Connection>
```


#### User Search

Find other bLinkup users (scoped by API key) by searching. The passed string will look for any matches in username or name.

Swift:

```swift
bLinkup.findUsers(query: String?, completion: @escaping (Result<[User], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.findUsers(query: String) 
//returns a List<User>
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

#### Contact Search

Finding users who are also in your contacts uses the platform contacts API to get the users contacts and send a list of phone numbers to the backend looking for matches. This is the easiest way to find all possible users without needing to search for each user.

Swift:

```swift
bLinkup.findContacts(completion: @escaping (Result<[ContactResult], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.findContacts() 
//returns a List<ContactResult>
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

#### Send Connection Request to User

Once you have found a user you wish to connect with, utilize the following call to send them a Connection Request.

Swift:

```swift
bLinkup.sendConnectionRequest(user: User, completion: @escaping (Result<ConnectionRequest, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.sendFriendRequest(friend: User) 
//returns a ConnectionRequest
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

#### Get list of Connection Request

Friend requests exist as connection requests, not a connection. The following call will return a list of all outstanding Connection Requests.

Swift:

```swift
bLinkup.getFriendRequests(completion: @escaping (Result<[ConnectionRequest], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.getFriendRequests() 
//returns a List<ConnectionRequest>
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

Users have the ability to accept or deny Connection Requests at their discretion. The following calls will provide the user with this option.

Swift:

```swift
bLinkup.acceptFriendRequest(_ req: ConnectionRequest, completion: @escaping (Result<ConnectionRequest, Error>) -> Void)
bLinkup.denyFriendRequest(_ req: ConnectionRequest, completion: @escaping (Result<ConnectionRequest, Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.acceptFriendRequest(request: ConnectionRequest) 
Blinkup.denyFriendRequest(request: ConnectionRequest) 
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

#### Update or delete Connection

If the user wishes to unfriend someone, the call below will allow them to delete the connection.

Swift:

```swift
bLinkup.updateConnection(_ connection: Connection, status: ConnectionStatus, 
                         completion: @escaping (Result<Connection, Error>) -> Void)
bLinkup.deleteConnection(_ connection: Connection, completion: @escaping (Result<Void, Error>) -> Void)
```

Kotlin:

```kotlin
[DEPRECATED] Use acceptFriendRequest and denyFriendRequest to update or remove a connection request
Blinkup.updateConnection(connection: Connection, status: ConnectionStatus)
Blinkup.deleteConnection(connection: Connection) 
```

Java:

```java
BlinkupWrapper.updateConnection(connection: Connection,status: ConnectionStatus, resultListener: new ResultListener<Unit>() {
        @override 
        public void onResult(Unit result) {
            
        } 
        
        @Override  
        public void onError(@NonNull Exception exception) {  
        
        }
})

BlinkupWrapper.deleteConnection(connection: Connection, resultListener: new ResultListener<Unit>() {
        @override 
        public void onResult(Unit result) {
            
        } 
        
        @Override  
        public void onError(@NonNull Exception exception) {  
        
        }
})
```

#### Blocking and unblocking users

In the event a user wishes to block another user from seeing them in any capacity, the following blocking functions will allow them to do so. They also have the ability to unblock a blocked user.

Swift:

```Swift
bLinkup.blockUser(_ user: User, completion: @escaping (Result<Block, Error>) -> Void)
bLinkup.deleteBlock(_ block: Block, completion: @escaping (Result<Void, Error>) -> Void)
```

Kotlin:

```Kotlin
Blinkup.blockUser(user: User) 
Blinkup.unblockUser(block: Block) 
```

Java:

```Java
BlinkupWrapper.blockUser(user: User, resultListener: new ResultListener<Block>() {
        @override 
        public void onResult(block: Block) {
            
        } 
        
        @Override  
        public void onError(@NonNull Exception exception) {  
        
        }
})

BlinkupWrapper.unblockUser(block: Block, resultListener: new ResultListener<Unit>() {
        @override 
        public void onResult(Unit result) {
            
        } 
        
        @Override  
        public void onError(@NonNull Exception exception) {  
        
        }
})
```

#### Getting list of blocked users

Use the following call in order to see a list of users blocked by the currently logged in user.

Swift:

```Swift
bLinkup.getBlocks(completion: @escaping (Result<[Block], Error>) -> Void) 
```

Kotlin:

```Kotlin
Blinkup.getBlocks() 
//returns a List<Block>
```

Java:

```Java
BlinkupWrapper.getBlocks(resultListener: new ResultListener<List<Block>>() {
        @override 
        public void onResult(List<Block> blocks) {
            
        } 
        
        @Override  
        public void onError(@NonNull Exception exception) {  
        
        }
})
```

#### bLinkpoints

bLinkpoints are points of interest at an event. These are set by you and are consumed in to the app by a JSON file which describes the point, positions it on a map image, and includes a URL to an image which can be shared when a user taps to send the bLinkpoint to a friend.

#### Get list of Events

In order to see a list of available events (as scope by API key), utilize the following call.

Swift:

```swift
bLinkup.getEvents(completion: @escaping (Result<[Place], Error>) -> Void)
```

Kotlin:

```kotlin
Blinkup.getEvents() 
//returns a List<Place>
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

#### Displaying the map

Displaying the map and putting the interactive points on the app is a UI element provided by bLinkup. Call the following API to display a modal which the user can interact with to dismiss or send invites to their friends.

Swift:

```swift
let controller = bLinkup.VenueMapViewController(Place)
navigationController.push(controller, animated: true)
```

Kotlin:

Place `VenueMapView` in your layout (or create it with code).
Get it's instance, for example

```kotlin
val map = findViewById<VenueMapView>(R.id."your_map_id")
```

and set the event you want to show the map for into `VenueMapView`

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

and set the event you want to show the map for into `VenueMapView`

```java
Place event = ...
map.setPlace(event)
```

#### Accessing the BlinkPoints

Swift:

```swift
place.blinkpoints
```

Kotlin:

```kotlin
place.blinkpoints
```

Java:

```java
place.getBlinkpoints()
```

#### Sending meet ups

Swift, Kotlin, Java:

This is handled by the map display function. When clicking on meetup spot on the map, it would offer to send a text message with invite to meet at that point.

#### Metadata
Metadata is used to determine the specific user in your system, which needs to receive specific push notification, for example, about new friend request or a new friend arriving to the event
Everything you would set as metadata for specific Blinkup user will be sent in the webhook so you can map it to proper user in your system

#### Getting current metadata

```kotlin
Blinkup.getMetadata
//returns List<Metadata>
```

#### Setting metadata

```kotlin
Blinkup.setMetadata(key, value)
```
#### Deleting metadata
Delete metadata with specific key:
```kotlin
Blinkup.deleteMetadata(key)
```

Delete all metadata:
```kotlin
Blinkup.deleteMetadata()
```
