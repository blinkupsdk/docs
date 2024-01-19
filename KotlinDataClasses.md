# Kotlin Data Classes

The following document contains a list of all Kotlin Data Classes and their associated fields contained within the SDK. To see where these objects might be used, please reference either the README or one of our sample apps.


## User
    
    id: String
    name: String
    phoneNumber: String
    emailAddress: String

## Connection

    id: String
    status: ConnectionStatus
    sourceUser: User
    targetUser: User

## ConnectionRequest

    id: String
    sourceUser: User
    targetUser: User
    phoneNumber: String
    insertedAt: String

## ConnectionStatus

    serialized: String

## Contact

    phoneNumer: String
    name: String

## ContactResult

    phoneNumer: String
    name: String
    userId: String

## Block

    id: String
    blocker: User
    blockee: User
    instertedAt: String

## Place

    id: String
    name: String
    latitude: Double
    longitude: Double
    radius: Int
    blinkpoints: ArrayList<Blinkpoint>
    geofence: String
    mapUrl: String

## Blinkpoint

    id: String
    name: String
    x: Int
    y: Int
    promoCardUrl: String

## Presence

    id: String
    user: User
    place: Place
    isPresent: Boolean
    instertedAt: String
    placeId: String