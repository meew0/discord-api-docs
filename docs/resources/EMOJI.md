# Emoji Resource

###### Emoji Object

| Field | Type | Description |
|-------|------|-------------|
| id | snowflake | emoji id |
| name | string | emoji name |
| roles | array of [role object](#DOCS_PERMISSIONS/role-object) ids | roles this emoji is active for |
| require_colons | boolean | whether this emoji must be wrapped in colons |
| managed | boolean | whether this emoji is managed |

## Get Emoji % GET /emojis/{emoji.id#DOCS_EMOJI/emoji-object}.png

Return a 302 redirect to the emoji file for the specified emoji id.
