# Voice

Voice connections operate in a similar fashion to the Gateway websocket, however they implement a different set of events and payloads, and utilize an extra UDP connection for actual voice data.

####### Voice Packet Structure
The voice packet is a 70 byte payload that begins with a 4 byte big-endian packed unsigned integer, which represents the clients 'ssrc'. The rest of the bytes in the payload are raw PCM data.

###### Voice Events

| Code | Name | Description |
|--------|----------|-----------------|
| 0 | Identify | used to begin a voice websocket connection |
| 1 | Select Protocol | used to select the voice protocol |
| 2 | Ready | used to complete the websocket handshake |
| 3 | Heartbeat | used to keep the websocket connection alive |
| 4 | Session Description | used to describe the session |
| 5 | Speaking | used to indicate which users are speaking |

## Connecting

Connecting to voice in Discord requires a [Gateway](#DOCS_GATEWAY) connection to begin. The first step in joining a voice channel is sending an OP4, Voice State Update to your connected gateway, with the following payload format:

```json
{
	"guild_id": "41771983423143937",
	"channel_id": "127121515262115840",
	"self_mute": false,
	"self_deaf": false
}
```

In response, the gateway should send an OP4, Voice State Update with the following payload:

```json
{
	"user_id": "104694319306248192",
	"session_id": "..."
}
``

Along with a VOICE_SERVER_UPDATE event, containing the following payload:

```json
{
	"token": "...",
	"guild_id": "41771983423143937",
	"endpoint": "smart.loyal.discord.gg"
}
```

Using these two payloads, the next step is to establish a separate voice websocket using the "endpoint" key passed in VOICE_SERVER_UPDATE (e.g. `wss://smart.loyal.discord.gg` in this example). Once connected, we then send an Identify (OP 0) with the following payload contents:

```json
{
	"server_id": "41771983423143937",
	"user_id": "104694319306248192",
	"session_id": "...",
	"token": "..."
}
```

The voice server should respond with OP2 Ready:

```json
{
	"ssrc": 1,
	"port": 1234,
	"modes": ["plain", "xsalsa20_poly1305"],
	"heartbeat_interval": 1
}
```

Using this information, we now need to establish our remote IP for UDP STUN. We do this by sending an empty voice data packet via UDP to port 80 on whatever the "endpoint" key we got in VOICE_SERVER_UPDATE, using the ssrc value that was passed in the voice-ready packet.




