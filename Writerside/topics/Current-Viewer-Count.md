# Chat Message

## Description

Represents a single chat message sent by a user. Sent as part of the CHAT webhook event.

## Fields

| Field | Description |
|---------|-------------|
| id | Unique identifier for the message |
| user | The User object of the message sender |
| clientId | Identifies the specific connection/session that sent the message |
| body | The message content, rendered as HTML |
| rawBody | The original, unrendered message text |
| visible | Whether the message is currently visible in chat |
| timestamp | When the message was sent |

## Source event

CHAT