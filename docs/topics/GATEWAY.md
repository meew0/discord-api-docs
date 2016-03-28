# Gateways

Gateways are Discords from of real-time communication over secure web-sockets. Clients will mostly receive events and data over the gateways, and send data over the REST API.

## Resources

### GET /gateway

Return an object with a single valid WSS URL. Clients should **not** cache this value, but instead should call this endpoint whenever they wish to reestablish a Gateway connection.

###### Example Response

```json
{
	"url": "wss://gateway-33.discord.gg/4"
}
```

## Using Gateways

From an implementation perspective, gateways are just secure web-sockets that send and receive JSON or ETF payloads.

## ETF/JSON

When initially creating and handshaking connections to the Gateway, a user can chose whether they wish to communicate over plain-text JSON, or binary [ETF](http://erlang.org/doc/apps/erts/erl_ext_dist.html). This selection is communicated to the gateway by sending the initial IDENTIFY packet in the format you wish to use. Payloads to the gateway are limited to a maximum of 4096 bytes sent, going over this will cause a connection termination with error code 4002.

### Rate Limiting

Unlike the HTTP API, Gateways do not provide a method for forced backoff or cooldown, but instead implement a hard limit on the number of messages sent over a period of time. Currently clients are allowed 120 events every 60 seconds, meaning you can send at a rate of up to 2 events per second. Clients who surpass this limit are immediately disconnected from the Gateway, and similarly to the HTTP API, repeat offenders will have their API access revoked. Clients are limited to one gateway connection per 5 seconds, if you hit this limit the Gateway will delay your connection until the cooldown has timed out.

> warn
> Clients may only update their game status once every 12 seconds.

## Payloads

### Sending Payloads

Packets sent from the client to the Gateway API are encapsulated within a [gateway payload object](#DOCS_GATEWAY/gateway-payload-object) and must have the proper OP code and data object set. The payload object can then be serialized in the format of choice, and sent over the websocket.

### Receiving Payloads

Receiving payloads with the Gateway API is slightly more complex than sending. The Gateway API has the option of sending payloads compressed using zlib, meaning your library _must_ detect and decompress these payloads.

###### Gateway OP Codes

| Code | Name | Description |
|--------|----------|-----------------|
| 0 | Dispatch | dispatches an event |
| 1 | Heartbeat | used for ping checking |
| 2 | Identify | used for client handshake |
| 3 | Status Update | used to update the client status |
| 4 | Voice State Update | used to join/move/leave voice channels |
| 5 | Voice Server Ping | used for voice ping checking |
| 6 | Resume | used to resume a closed connection |
| 7 | Redirect | used to redirect clients to a new gateway |
| 8 | Request Guild Members | used to request guild members |

###### Gateway Payload Object

| Field | Type | Description |
|-------|------|-------------|
| op | integer | the [opcode](#DOCS_GATEWAY/opcodes) for this payload |
| d | object | any data to be sent along with the payload |

###### Gateway Dispatch Payload Object

| Field | Type | Description |
|-------|------|-------------|
| op | integer | always set to 0 |
| s | integer | sequence number, used for reconnecting |
| t | string | the event name for this payload |
| d | object | event data |

## Gateway Versions

Out of Services versions are versions who's subset of changes compared to the most version have been completely removed from the Gateway. Using these versions will break your client, and result in undefined behavior.

| Version | Changes | Out of Service |
|------------|--------------|----------------|
| 3 | first official API version | no |

## Events

### Ready

The ready event is dispatched when a client has completed the handshake with the gateway. The ready event is the largest and most complex event the gateway will send, as it contains all the state required for a client to begin interacting with the rest of the platform.

###### Ready Event Fields (READY)

| Field | Type | Description |
|-------|------|-------------|
| v | integer | [gateway version](#DOCS_GATEWAY/gateway-versions) |
| user | object | [user object](#DOCS_USER/user-object) (with email information) |
| private_channels | array | array of [DM channel](#DOCS_CHANNEL/dm-channel-object) objects |
| guild_ids | array | array of snowflake guild ids |
| read_state | array | array of [read state](#DOCS_CHANNEL/read-state-object) objects |

### Channel Create (CHANNEL_CREATE)

Sent when a new channel is created, relevant to the current user. The inner payload is a [DM](#DOCS_CHANNEL/dm-channel-object) or [Guild](#DOCS_CHANNEL/guild-channel-object) channel object.

### Channel Delete (CHANNEL_DELETE)

Sent when a channel relevant to the current user is deleted. The inner payload is a [DM](#DOCS_CHANNEL/dm-channel-object) or [Guild](#DOCS_CHANNEL/guild-channel-object) channel object.

### Channel Update (CHANNEL_UPDATE)

Sent when a channel is updated. The inner payload is a [guild channel](#DOCS_CHANNEL/guild-channel-object) object.

### Guild Ban Add (GUILD_BAN_ADD)

Sent when a user is banned from a guild. The inner payload is a [user](#DOCS_USER/user-object) object.

### Guild Ban Remove (GUILD_BAN_REMOVE)

Sent when a user is unbanned from a guild. The inner payload is a [user](#DOCS_USER/user-object) object.

### Guild Create (GUILD_CREATE)

Sent when a guild becomes unavailable after a partial guild outage. Because of the way Discord is architected, it's possible for a set of guilds to go offline (or "unavailable"), while the vast majority of them stay online. When guilds are in an offline state, no actions will be received or sent about them until they come back online, which is signified by this event. The inner payload is a [guild](#DOCS_GUILD/guild-object) object.

### Guild Delete (GUILD_DELETE)

Sent when a guild becomes unavailable during a guild outage. See GUILD_CREATE for more information about how to handle this event.

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | id of the guild |
| unavailable | boolean | whether the guild is unavailable, should always be true |

### Guild Integrations Update (GUILD_INTEGRATIONS_UPDATE)

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | id of the guild whose integrations where updated |

### Guild Member Add (GUILD_MEMBER_ADD)

Sent when a new user joins a guild. The inner payload is a [guild member](#DOCS_GUILD/guild-member-object) object, with an extra `guild_id` key.

### Guild Member Remove (GUILD_MEMBER_REMOVE)

Sent when a user is removed from a guild (leave/kick/ban).

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | the id of the guild |
| user | a [user](#DOCS_USER/user-object) object | the user who was removed |

### Guild Member Update (GUILD_MEMBER_UPDATE)

Sent when a guild member is updated.

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | the id of the guild |
| roles | array of [role](#DOCS_PERMISSIONS/role-object) objects | user roles |
| user | a [user](#DOCS_USER/user-object) object | the user |

### Guild Role Create (GUILD_ROLE_CREATE)

Sent when a guild role is created.

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | the id of the guild |
| role | a [role](#DOCS_PERMISSIONS/role-object) object | the role created |

### Guild Role Delete (GUILD_ROLE_DELETE)

Sent when a guild role is deleted

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | id of the guild |
| role_id | snowflake | id of the role |

### Guild Role Update (GUILD_ROLE_UPDATE)

Sent when a guild role is updated.

| Field | Type | Description |
|-------|------|-------------|
| guild_id | snowflake | the id of the guild |
| role | a [role](#DOCS_PERMISSIONS/role-object) object | the role created |

### Guild Update (GUILD_UPDATE)

Sent when a guild is updated. The inner payload is a [guild](#DOCS_GUILD/guild-object) object.

### Message Create (MESSAGE_CREATE)

Sent when a message is created. The inner payload is a [message](#DOCS_CHANNEL/message-object) object.

### Message Update (MESSAGE_UPDATE)

Sent when a message is updated. The inner payload is a [message](#DOCS_CHANNEL/message-object) object. Unlike creates, message updates may contain only a subset of the full message object payload (but will always contain an id and channel_id).

### Message Delete (MESSAGE_DELETE)

Sent when a message is deleted.

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | the id of the message |
| channel_id | snowflake | the id of the channel |

### Presence Update (PRESENCE_UPDATE)

Sent when a users presence is updated.

| Field | Type | Description |
|-------|------|-------------|
| user | [user](#DOCS_USER/user-object) object | the user presence is being updated for |
| roles | array of snowflakes | roles this user is in |
| game | object | null, or an object containing one key of "name" |
| guild_id | snowflake | id of the guild |
| status | string | either "idle", "online" or "offline" |

### Typing Start (TYPING_START)

Sent when a user starts typing in a channel.

| Field | Type | Description |
|-------|------|-------------|
| channel_id | snowflake | id of the channel |
| user_id | snowflake | id of the user |
| timestamp | timestamp | when the user started typing |

### User Settings Update (USER_SETTINGS_UPDATE)

Sent when the current user updates their settings. Inner payload is a [user settings](#DOCS_USER/user-settings-object) object.

### Voice State Update (VOICE_STATE_UPDATE)

Sent when someone joins/leaves/moves voice channels.

| Field | Type | Description |
|-------|------|-------------|
| user_id | snowflake | id of the user |
| guild_id | snowflake | id of the guild |
| channel_id | snowflake | id of the channel |
| session_id | string | id of the session |
| self_mute | boolean | whether the user is muted |
| self_deaf | boolean | whether the user is deafened |
| mute | boolean | whether the user is server-muted |
| deaf | boolean | whether the user is server-deafened |