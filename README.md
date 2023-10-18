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
init() {bLinkupSDK.configure(clientId: "YOUR_API_KEY_HERE")}
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
kSDK?.register(phone: phone, code: code, name: nil, email: nil, completion: { ...
```

Kotlin

```kotlin
var register = Register(sharedPreferenceManager: SharedPreferenceManager)
register.register(phoneNumber: String)
```

### User Login

To login there are two functions which need to be called.

1. Send the code to the user's phone number
2. Verify the code matches

Swift:

```swift
func requestCode(phone: String, completion: @escaping Completion<NextLoginStep>)
let params = ["phone_number": phone]

func confirmCode(phone: String, code: String, completion: @escaping Completion<User>)

func login(phone: String, code: String, completion: @escaping Completion<APILoginResponse>) {
let params = ["phone_number": phone, "verification_code": code]
```

Kotlin

```kotlin
val client = OkHttpClient()
val oneTimePasscode = OneTimePasscode(client, sharedPreferenceManager)
oneTimePasscode.sendSmsVerification()

smsVerification = SmsVerification(sharedPreferenceManager)
smsVerification.submitPhoneNumber(phoneNumber)
```

### Update User Profile

Swift:

```swift
NeedsUpdate - bLinkup.userData(firstName: String, lastName: String, username: String): User
```

Kotlin

```kotlin
var userProfile: UserProfile
userProfile.updateProfile(firstName: String, lastName: String, username: String): User
```

## Core presence loop

### Is at event

Swift:

```swift
await bLinkup.isAtEvent(isAtEvent: bool)
```

Kotlin

```kotlin
val locationData = locationData()
locationData.sendLocationData(location: Location)
```

## Connection Management

### Get current list of friends

Swift:

```swift
func getConnections(completion: @escaping Completion<[Connection]>) {kSDK?.connections(completion: completion)}
```

Kotlin

```kotlin
val checkFriends = CheckFriends()
val friendList = checkFriends.retrieveFriendsList(this@CheckFriendsT): List<String>
```

### Friends at event

The core value of bLinkup is getting a list of a user's friends who are at a particular event.

Swift:

```swift
func getFriendsAtPlace(_ place: Place, completion: @escaping Completion<[Presence]>) {kSDK?.presenceAtPlace(pid: place.id, completion: completion)
```

Kotlin

```kotlin

```

### Friend Search

Find other bLinkup users (scoped by API key) by searching. The passed string will look for any matches in username or name.

Swift:

```swift
func findFriends(query: String?, completion: @escaping Completion<[User]>) {kSDK?.searchFriends(query: query, completion: completion)}

func findContacts(completion: @escaping Completion<[Contact]>) {completion(.failure(SDKError.willBeSoon))}
```

Kotlin

```kotlin
friendSearch = FriendSearch(sharedPreferenceManager, client)
friendSearch.performSearch(query: String): List<User>
```

### Contact Search

Finding users who are also in your contacts uses the platform contacts API to get the users contacts and send a list of phone numbers to the backend looking for matches. This is the easiest way to find all possible users without needing to search for each user.

Swift:

```swift
func findFriends(query: String?, completion: @escaping Completion<[User]>) { kSDK?.searchFriends(query: query, completion: completion)}

func findContacts(completion: @escaping Completion<[Contact]>) {(.failure(SDKError.willBeSoon))}
```

Kotlin

```kotlin
contactSearch = ContactSearch(this)
sharedPreferenceManager = SharedPreferenceManager(this)
val phoneNumbers = contactSearch.fetchContacts() : ArrayList<Pair<String, String>>
contactSearch.sendPostRequest(contactList: phoneNumbers, authToken: String) Array<Contact>

```

### Send Connect request to User

Swift:

```swift
func sendConnectionRequest(user: User, completion: @escaping Completion<ConnectionRequest>) {kSDK?.createConnectionRequest(uid: user.id,completion: completion)}

```

Kotlin

```kotlin
val sendFriendRequest = sendFriendRequest
sendFriendRequest.sendFriendRequest(context: Context, userId: String)
```

### Accept and deny request

Swift:

```swift
func acceptConnectionRequest(_ req: ConnectionRequest, completion: @escaping Completion<Connection>

func denyConnectionRequest(_ req: ConnectionRequest,completion: @escaping Completion<Void>)
```

Kotlin

```kotlin
val friendResponse = FriendResponse
friendResponse.acceptFriendRequest(context: Context, binding: ActivityFriendSearchBinding): Unit

val deleteFriend = DeleteFriend
deleteFriend.deleteFriend(context: Context, binding: ActivityFriendSearchBinding): Unit

```

## bLinkpoints

bLinkpoints are points of interest at an event. These are set by you and are consumed in to the app by a JSON file which describes the point, positions it on a map image, and includes a URL to an image which can be shared when a user taps to send the bLinkpoint to a friend.

### Displaying the map

Displaying the map and putting the interactive points on the app is a UI element provided by bLinkup. Call the following API to display a modal  which the user can interact with to dismiss or send invites to their friends.

### Sending meet ups
