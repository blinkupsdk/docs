# docs

Getting started and best practices of using the bLinkup SDK.

Use the bLinkup SDK to do these three main things

1. Connect with friends on the app
2. Notify friends when they're at the event (using smart tracking & proximity)
3. Easily send invites to locations on a venue map

To achieve these functions the following steps need to be taken:

- Sign-up/ Login, using phone number and SMS code
- Find friends, by searching name or matching contacts
- Send and accept friend requests
- Get a list of friends and notify users about which friends are at an event
- Send invites to venue specific points of interest
- Get user autimatically registered at events when they enter the event area

## Getting an API Key

Email Will Bott at [willbott@blinkupapp.com](mailto:willbott@blinkupapp.com) to start the process of getting an API key for your app.

## Import Blinkup module

```javascript
const {BlinkupModule} = NativeModules;
```

## Initialization

When your app starts initialize bLinkup with your other packages. This should only be done once at app launch.

```javascript
BlinkupModule.init('Your Client ID');
```

## Login/Registration
Login and registration are packed into a single procedure in bLinkup SDK. You would just need to check if the user details (name and email) are already filled-in

### Request confirmation code

```javascript
try {
	message = await BlinkupModule.requestCode('+123456789');
} catch (e) {
	console.error(e);
}
```
`message` is just for information and can be ignored. It would say something like `Verification code sent`

### Confirm with code

```javascript
try {
	user = await BlinkupModule.confirmCode('code');
} catch (e) {
	console.error(e);
}
```
After this you should perform one more operation before using other features of bLinkup SDK

### Updating user details
In case if it is the first time the user is logged into bLinkup, you should provide the user details (name and email)
To check that, simply call `detailsRequired = BlinkupModule.isUserDetailsRequired();`
If the details are required, provide them with the following code
```javascript
try {
	user = await BlinkupModule.updateUser('name', 'email');
} catch (e) {
	console.error(e);
}
```

## Using stored session

You don't have to log user in every time your app is started. bLinkup SDK stores the user session token and can reuse it. 
To know if you need to perform login, or can use the stored session, use the following code
```javascript
isLoginRequired = BlinkupModule.isLoginRequired();
```

## Interacting with other users

### Searching for the users

```javascript
try {
	users = await BlinkupModule.findUsers('search query');
} catch (e) {
	console.error(e);
}
```
### Get friends list

```javascript
try {
	connections = await BlinkupModule.getFriendList();
} catch (e) {
	console.error(e);
}
```

### Get friend requests

```javascript
try {
	requests = await BlinkupModule.getFriendRequests();
} catch (e) {
	console.error(e);
}
```

### Accept friend request

```javascript
try {
	await BlinkupModule.acceptFriendRequest(request);
} catch (e) {
	console.error(e);
}
```

### Denyfriend request

```javascript
try {
	await BlinkupModule.denyFriendRequest(request);
} catch (e) {
	console.error(e);
}
```

### Update connection status (block/unblock users)

```javascript
try {
	await BlinkupModule.updateConnection(connection, newStatus);
} catch (e) {
	console.error(e);
}
```

### Send friend request

```javascript
try {
	connection = await BlinkupModule.sendFriendRequest(user);
} catch (e) {
	console.error(e);
}
```

## Interacting with events
### Register/unregister user at event

```javascript
try {
	await BlinkupModule.setUserAtEvent(true/false, place);
} catch (e) {
	console.error(e);
}
```

### Check if user is registered at event

```javascript
try {
	isAtEvent = await BlinkupModule.isUserAtEvent(place);
} catch (e) {
	console.error(e);
}
```

### List users registered at event

```javascript
try {
	users = await BlinkupModule.getUsersAtEvent(place);
} catch (e) {
	console.error(e);
}
```
