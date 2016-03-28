# Guild Resource

Guilds in Discord represent a collection of users and channels into an isolated "server".

### Guild Object

###### Guild Object

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | guild id |
| name | string | guild name (2-100 characters) |
| icon | string | icon hash |
| splash | string | splash hash |
| owner_id | snowflake | id of owner |
| region | string | {voice_region.id} |
| afk\_channel\_id | snowflake | id of afk channel | 
| afk_timeout | integer | afk timeout in seconds |
| embed_enabled | bool | is this guild embeddable (e.g. widget) |
| embed_channel_id | snowflake | id of embedded channel |
| verification_level | integer | level of verification |
| roles | array | array of [role](#DOCS_PERMISSION/role-object) objects |
| emojis | array | array of [emoji](#DOCS_EMOJI/emoji-object) objects |
| features | array | array of guild features |

###### Example Guild Object

```json
{
	"id": "41771983423143937",
	"name": "Discord Developers",
	"icon": "SEkgTU9NIElUUyBBTkRSRUkhISEhISEh",
	"splash": null,
	"owner_id": "80351110224678912",
	"region": "us-east",
	"afk_channel_id": "42072017402331136",
	"afk_timeout": 300,
	"embed_enabled": true,
	"embed_channel_id": "41771983444115456",
	"verification_level": 1,
	"roles": [...],
	"emojis": [...],
	"features": ["INVITE_SPLASH"]	
}
```

### Guild Embed Object

###### Guild Embed Object

| Field | Type | Description |
|-------|------|-------------|
| enabled | bool | if the embed is enabled |
| channel_id | snowflake | the embed channel id |

###### Example Guild Embed Object

```json
{
	"enabled": true,
	"channel_id": "41771983444115456"
}
```

### Guild Member Object

###### Guild Member Object

| Field | Type | Description |
|-------|------|-------------|
| user | object | [user](#DOCS_USER/user-object) object |
| roles | array | array of [role](#DOCS_PERMISSION/role-object) object id's |
| joined_at | datetime | date the user joined the guild |
| deaf | bool | if the user is deafened |
| mute | bool | if the user is muted |

###### Example Guild Member Object

```json
{
	"user": {...},
	"roles": [...],
	"joined_at": "2015-04-26T06:26:56.936000+00:00",
	"deaf": false,
	"mute": false
}
```

### Integration Object

###### Integration Object

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | integration id |
| name | string | integration name |
| type | string | integration type (twitch, youtube, etc) |
| enabled | boolean | is this integration enabled |
| syncing | boolean | is this integration syncing |
| role_id | snowflake | id that this integration uses for "subscribers" |
| expire_behavior | integer | the behavior of expiring subscribers |
| expire_grace_period | integer | the grace period before expiring subscribers |
| user | [user](#DOCS_USER/user-object) object | user for this integration |
| account | [account](#DOCS_GUILD/integration-account-object) object | integration account information |
| synced_at | timestamp | when this integration was last synced |

### Integration Account Object

###### Integration Account Object

| Field | Type | Description |
|-------|------|-------------|
| id | string | id of the account |
| name | string | name of the account |

## Create Guild % POST /guilds

Create a new guild. Returns a [guild](#DOCS_GUILD/guild-object) object on success.

>warn
> This endpoint is only available for whitelisted bots. If you believe you have a legitimate use case for
> automating guild creation, please contact us at support@discordapp.com and we'll look into whitelisting you.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| name | string | name of the guild (2-100 characters) |
| region | string | {voice_region.id} for voice |
| icon | string | base64 128x128 jpeg image for the guild icon |

## Get Guild % GET /guilds/{guild.id#DOCS_GUILD/guild-object}

Return a [guild](#DOCS_GUILD/guild-object) object for the given id.

## Modify Guild % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}

Modify a guilds settings. Returns a [guild](#DOCS_GUILD/guild-object) object on success.

>info
> All parameters to this endpoint are optional

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| name | string | guild name |
| region | string | guild {voice_region.id} |
| verification_level | integer | guild verification level |
| afk\_channel\_id | snowflake | id for afk channel |
| afk_timeout | integer | afk timeout in seconds |
| icon | string | base64 128x128 jpeg image for the guild icon |
| owner_id | snowflake | user id to transfer guild ownership to (must be owner) |
| splash | string | base64 128x128 jpeg image for the guild splash (VIP only) |

## Delete Guild % DELETE /guilds/{guild.id#DOCS_GUILD/guild-object}

Delete a guild permanently. User must be owner. Returns a [guild](#DOCS_GUILD/guild-object) object on success.

## Get Guild Channels % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/channels

Return a list of guild [channel](#DOCS_CHANNEL/guild-channel-object) objects.

## Create Guild Channel % POST /guilds/{guild.id#DOCS_GUILD/guild-object}/channels

Create a new [channel](#DOCS_CHANNEL/guild-channel-object) object for the guild. Requires the 'MANAGE_CHANNELS' permission. Returns the new [channel](#DOCS_CHANNEL/guild-channel-object) object on success.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| name | string | channel name (2-100 characters) |
| type | string | "voice" or "text" |
| bitrate | integer | the bitrate (in bits) of the voice channel (voice only) |

## Modify Guild Channel % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}/channels

Batch modify the position of channels in a guild. Requires the 'MANAGE_CHANNELS' permission. This endpoint takes a JSON array of parameters in the following format:

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | channel id |
| position | integer | position of channel |

## Get Guild Member % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/members/{user.id#DOCS_USER/user-object}

Return a [guild member](#DOCS_GUILD/guild-member-object) object for the specified user.

## List Guild Members % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/members

Return a list of [guild member](#GUILD/guild-member-object) objects that are members of the guild.

>info
> All parameters to this endpoint are optional

###### HTTP Params

| Field | Type | Description | Default |
|-------|------|-------------|---------|
| limit | integer | max number of members to return (1-1000) | 1 |
| offset | integer | the 0-indexed offset to start at | 0 | 

## Modify Guild Member % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}/members/{user.id#DOCS_USER/user-object}

Modify attributes of a [guild member](#DOCS_GUILD/guild-member-object).

>info
> When moving members to channels, the API user _must_ have permissions to both connect to the
> channel and have the MOVE_MEMBERS permission.


###### JSON Params

| Field | Type | Description | Permission |
|-------|------|-------------|------------|
| roles | array | array of roles the member is assigned | MANAGE_ROLES |
| mute | bool | if the user is muted | MUTE_MEMBERS |
| deaf | bool | if the user is deafened | DEAFEN_MEMBERS |
| channel_id | snowflake | id of channel to move user to (if they are connected to voice) | MOVE_MEMBERS |

## Remove Guild Member % DELETE /guilds/{guild.id#DOCS_GUILD/guild-object}/members/{user.id#DOCS_USER/user-object}

Remove a member from a guild. Requires 'KICK_MEMBERS' permission.

## Get Guild Bans % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/bans

Return a list of [user](#DOCS_USER/user-object) objects that are banned from this guild. Requires the 'BAN_MEMBERS' permission.

## Create Guild Ban % PUT /guilds/{guild.id#DOCS_GUILD/guild-object}/bans/{user.id#DOCS_USER/user-object}

Create a guild ban, and optionally delete previous messages sent by the banned user. Requires the 'BAN_MEMBERS' permission.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| delete-message-days | integer | number of days to delete messages for (0-7) |

## Remove Guild Ban % DELETE /guilds/{guild.id#DOCS_GUILD/guild-object}/bans/{user.id#DOCS_USER/user-object}

Remove the ban for a user. Requires the 'BAN_MEMBERS' permissions

## Get Guild Roles % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/roles

Return a list of [role](#DOCS_PERMISSION/role-object) objects for the guild. Requires the 'MANAGE_ROLES' permission.

## Create Guild Role % POST /guilds/{guild.id#DOCS_GUILD/guild-object}/roles

Create a new empty [role](#DOCS_PERMISSION/role-object) object for the guild. Requires the 'MANAGE_ROLES' permission.

>warn
> This endpoint only creates a blank role, it does not allow you to set attributes for the role on creation. Instead, you must create the role and then modify it with a PATCH request.


## Batch Modify Guild Role % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}/roles

Batch modify a set of [role](#DOCS_PERMISSION/role-object) objects for the guild. Requires the 'MANAGE_ROLES'
permission.

>warn
> This endpoint should *only* be used for modifying a batch set of roles.

This endpoint takes a JSON array of parameters in the following format:

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | the id of the role (cannot be modified, only for reference) |
| name | string | name of the role |
| permissions | integer | bitwise of the enabled/disabled permissions |
| position | integer | sorting position of the role |
| color | integer | RGB color value |
| hoist | bool | should this role be displayed separately in the sidebar |

## Modify Guild Role % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}/roles/{role.id#DOCS_PERMISSION/role-object}

Modify a guild role. Requires the 'MANAGE_ROLES' permission. Returns the [role](#DOCS_PERMISSION/role-object) on success.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | the id of the role (cannot be modified, only for reference) |
| name | string | name of the role |
| permissions | integer | bitwise of the enabled/disabled permissions |
| position | integer | sorting position of the role |
| color | integer | RGB color value |
| hoist | bool | should this role be displayed separately in the sidebar |

## Delete Guild Role % DELETE /guilds/{guild.id#DOCS_GUILD/guild-object}/roles/{role.id#DOCS_PERMISSION/role-object}

Delete a guild role. Requires the 'MANAGE_ROLES' permission. Returns the [role](#PERMISSION/role-object) on success.

## Get Guild Prune Count % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/prune

Return an object with one 'pruned' key indicating the number of members that would be removed in a prune operation. Requires the 'KICK_MEMBERS' permission.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| days | integer | number of days to count prune for (1-7) |

## Begin Guild Prune % POST /guilds/{guild.id#DOCS_GUILD/guild-object}/prune

Begin a prune operation. Requires the 'KICK_MEMBERS' permission.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| days | integer | number of days to prune (1-7) |

## Get Guild Voice Regions % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/regions

Return a list of [voice region](#DOCS_VOICE/voice-region-object) objects for the guild. Unlike the similar `/voice` route, this returns VIP servers when the guild is VIP-enabled.

## Get Guild Invites % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/invites

Return a list of [invite](#DOCS_CHANNEL/invite-object) objects for the guild. Requires the 'MANAGE_GUILD' permission.

## Get Guild Integrations % GET /guilds/{guild.id#DOCS_GUILD/guild-object}/integrations

Return a list of [integration](#DOCS_GUILD/integration-object) objects for the guild. Requires the 'MANAGE_GUILD' permission.

## Create Guild Integration % POST /guilds/{guild.id#DOCS_GUILD/guild-object}integrations

Attach an [integration](#DOCS_GUILD/integration-object) object from the current user to the guild. Requires the 'MANAGE_GUILD' permission.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| type | string | the integration type |
| id | snowflake | the integration id |

## Modify Guild Integration % PATCH /guilds/{guild.id#DOCS_GUILD/guild-object}/integrations/{integration.id#DOCS_GUILD/integration-object}

Modify the behavior and settings of a [integration](#DOCS_INTEGRATION/integration-object) object for the guild. Requires the 'MANAGE_GUILD' permission.

###### JSON Params

| Field | Type | Description |
|-------|------|-------------|
| expire_behavior | integer | the behavior when an integration subscription lapses (see the [integration](#DOCS_INTEGRATION/integration-object) object documentation) |
| expire\_grace\_period | integer | period (in seconds) where the integration will ignore lapsed subscriptions |
| enable_emoticons | bool | whether emoticons should be synced for this integration (twitch only currently) |

## Delete Guild Integration % DELETE /guilds/{guild.id#DOCS_GUILD/guild-object}/integrations/{integration.id#DOCS_INTEGRATION}

Delete the attached [integration](#DOCS_INTEGRATION/integration-object) object for the guild. Requires the 'MANAGE_GUILD' permission.

## Sync Guild Integration % POST /guilds/{guild.id}/integrations/{integration.id}/sync

Sync an integration. Requires the 'MANAGE_GUILD' permission.

## Get Guild Embed % GET /guilds/{guild.id}/embed

Return a [guild embed](#DOCS_GUILD/guild-embed-object) object. Requires the 'MANAGE_GUILD' permission.

## Modify Guild Embed % PATCH /guilds/{guild.id}/embed

Modify a [guild embed](#DOCS_GUILD/guild-embed-object) object for the guild. All attributes may be passed in with JSON and modified. Requires the 'MANAGE_GUILD' permission.
