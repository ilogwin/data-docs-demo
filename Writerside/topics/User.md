# User

## Description

Represents a chat user connected to an Owncast server. This object appears inside several webhook events, such as CHAT and USER_JOINED.

## Fields

| Field | Description |
|---------|-------------|
| id | Unique identifier for the user |
| displayName | The user's current chat display name |
| displayColor | Numeric value used to assign the user's chat color |
| createdAt | Timestamp when the user first connected |
| previousNames | List of display names the user has previously used |
| nameChangedAt | Timestamp of the user's last name change |
| isBot | Whether the user is a bot account |
| authenticated | Whether the user is authenticated |

## Source event

CHAT, USER_JOINED, NAME_CHANGE