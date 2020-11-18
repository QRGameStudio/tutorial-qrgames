# Multiplayer

Currently, the QR games multiplayer is realized by utilizing an amazing library called [PeerJS](https://peerjs.com/). As the name suggests, the connection in QR multiplayer is fully peer to peer and the server is only used to negotiate connections between clients. QR games have its own instance of the PeerJS server.

This may change in the future, because we would like to eliminate the middle server completely.

## How it works

The multiplayer is not active until the user explicitly approves, either by using in game dialog or in settings.

If the client is connecting for the first time, it asks the negotiating server for its id, which is an uuid4. The client saves the id generated for it and from that point on uses it when connecting to the server. When the client wants to connect to another client, he must know his id, which is then sent to the server. The server then tells the client how to connect to another client.

When a new connection between two clients is made, it is checked that both clients are currently playing a game with same game id. This is evaluated to true if both clients are playing a game without id. If the ids of the games do not match, then the connection is closed.

Next step is to present the user with a dialog if he wants to accept a connection. If the id matches one of the friend ids, then the friends name is displayed. Only if the user approves the connection, the code will return new connection instance. Otherwise, the connection is closed.

## API

This API references the ideal behavior, the implementation in the players can slightly differ, but the functionality should be the same. The code for the multiplayer inside debugging player can be found [here](https://github.com/QRGameStudio/web-qrpr.eu/blob/master/lib/GMultiplayer.js).

### Object GFriends

This object is used for storing information about devices known ids and their names. It has following functions when instantiated:

#### getFriends()

Async. Returns a dictionary in format `name: str -> id: str` of all saved friends as promise.

#### addFriend(id: string, name: string)

Saves a new friend with given id and name. Async.

#### removeFriend(name)

Removes friend by name. Async.

#### showFriendsModal(options?)

Async. The options are, with the default values:

```json
{
    allowAdd: true,
    allowDelete: false,
    allowSelect: false
}
```

This functions shows a modal with a list of friends and the behavior of the modal can be modified by using the options:

- **allowAdd** - show button to add a friend, which opens aditional modal for inputing friend's name and id
- **allowDelete** - show button to remove an existing friend
- **allowSelect** - when the friend's name is clicked/selected, the modal closes and the function returns selected friend's name

When the modal is closed and no friend was selected, then the function shall return `null`.

### GMP

This object is used for creating connections. It has following functions when instantiated:

#### isEnabled()

Async. Returns a bool that states if the user has allowed the multiplayer. 

#### getSavedId()

Async. Returns the saved id of local device. Null if no device id was set before.

#### setCurrentGameId(id: string)

Force replaces id of the currently running game with another. If set to `DEBUG` then all connections are allowed and no game id checking is performed.

#### getMe()

Async. If the multiplayer is not allowed yet, asks user with modal if he wishes to enable the multiplayer. If the users answers no, returns null.

If an connection to the negotiating server is already created and alive, returns it.

Otherwise creates a new connection to the server, saves assigned id and returns the connection.

#### onConnection(callback: () => conn, dataCallback: () => any)

Async. Waits for the connections from another client. If a new connection is received, checks if ids of the game match. If not, closes it.

Otherwise shows a dialog to the user if he wants to allow the connection. If the user does not approve, closes it.

When the connection is approved, the `callback(newConnectionInstance)` is called. When the connection sends some data, then `dataCallback(data)` is called. Beware that both callbacks can be executed many times, for each new connection and new data respectively.

Calls `dataCallback(null)` when the connection is closed.

#### connect(id: str, dataCallback: () => any)

Async. Tries to create a new connection to the given partner id. If successful, returns the connection instance. Calls `dataCallback(data)` when new data arrives. Calls `dataCallback(null)` when the connection is closed.