# bLinkup Objects

The following document contains a list of all objects to be return from the bLinkup database with their respective fields. To see where these objects might be used, please reference either the README or one of our sample apps.


## User
    
    id: String
    name: String
    phone_number: String
    email_address: String

## Connection

    id: String
    status: ConnectionStatus
    source_user: User
    target_user: User

## ConnectionRequest

    id: String
    source_user: User
    target_user: User
    phone_number: String
    inserted_at: String

## ConnectionStatus

    serialized: String

## Contact

    phone_numer: String
    name: String

## ContactResult

    phone_numer: String
    name: String
    user_id: String

## Block

    id: String
    blocker: User
    blockee: User
    insterted_at: String

## Place

    id: String
    name: String
    latitude: Double
    longitude: Double
    radius: Int
    blinkpoints: ArrayList<Blinkpoint>
    geofence: String
    map_url: String

## Blinkpoint

    id: String
    name: String
    x: Int
    y: Int
    promo_card_url: String

## Presence

    id: String
    user: User
    place: Place
    is_present: Boolean
    insterted_at: String
    place_id: String