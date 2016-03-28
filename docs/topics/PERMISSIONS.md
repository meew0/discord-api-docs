# Permissions

Permissions in Discord are a way to limit and grant certain abilities to users. A set of base permissions can be configured at the guild level for different roles, when these roles are attached to users they grant or revoke specific privileges within the guild. Along with the global guild-level permissions, Discord also supports role overwrites which can be set at the channel level allowing customization of permissions on a per-role, per-channel basis.

Permissions in Discord are stored within a 53-bit integer and are calculated using bitwise operations. Permissions for a user in a given channel can be calculated by ANDing together their guild-level role permission integers, and their channel-level role permission integers. For more information about bitwise operations and flags, see [this page](https://en.wikipedia.org/wiki/Bit_field). 

###### Bitwise Permission Flags

| Permission | Value | Description |
|------------|-------|-----------|
| CREATE\_INSTANT\_INVITE | 1 | Allows creating of instant invites |
| KICK_MEMBERS | 2 | Allows kicking members |
| BAN_MEMBERS | 4 | Allows banning members |
| MANAGE_ROLES | 8 | Allows management and editing of roles |
| MANAGE_CHANNELS | 16 | Allows management and editing of channels |
| MANAGE_GUILD | 32 | Allows management and editing of the guild |
| READ_MESSAGES | 1024 | Allows reading messages in a channel. The channel will not appear for users without this permission |
| SEND_MESSAGES | 2048 | Allows for sending messages in a channel. |
| SEND\_TTS_MESSAGES | 4096 | Allows for sending of `/tts` messages |
| MANAGE_MESSAGES  | 8192 | Allows for deleting messages |
| EMBED_LINKS | 16384 | Links sent by this user will be auto-embedded |
| ATTACH_FILES | 32768 | Allows for uploading images and files |
| READ\_MESSAGE_HISTORY | 65536 | Allows for reading messages history |
| MENTION_EVERYONE | 131072 | Allows for using the `@everyone` tag to notify all users in a channel |
| CONNECT | 1048576 | Allows for joining of a voice channel |
| SPEAK | 2097152 | Allows for speaking in a voice channel |
| MUTE_MEMBERS | 4194304 | Allows for muting members in a voice channel |
| DEAFEN_MEMBERS | 8388608 | Allows for deafening of members in a voice channel |
| MOVE_MEMBERS | 16777216 | Allows for moving of members between voice channels |
| USE_VAD | 33554432 | Allows for using voice-activity-detection in a voice channel |

###### Role Object

Roles represent a set of permissions attached to a group of users. Roles have unique names, colors, and can be "pinned" to the side bar, causing their members to be listed separately. Roles are unique per guild, and can have separate permission profiles for the global context (guild) and channel context.

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | role id |
| name | string | role name |
| color | integer | RGB color value |
| hoist | bool | if this role is pinned in the user listing |
| position | integer | position of this role |
| permissions | integer | bitwise permission set |
| managed | bool | whether this role is managed by an integration |


###### Role Object Example

```json
{
	"id": "41771983423143936",
	"name": "WE DEM BOYZZ!!!!!!",
	"color": 3447003,
	"hoist": true,
	"position": 1,
	"permissions": 66321471,
	"managed": false
}
```