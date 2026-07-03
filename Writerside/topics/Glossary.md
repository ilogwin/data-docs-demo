# Glossary

## Webhook

An HTTP request that Owncast sends automatically to a configured URL when a specific event happens on the server.

## Event Type

A label identifying what kind of event a webhook payload describes (for example, CHAT or STREAM_STARTED).

## eventData

The part of a webhook payload that contains the specific details of an event. Its structure depends on the event type.

## clientId

A number identifying a single connection to Owncast. A user can have multiple clientIds if they connect from several devices or browsers at once.

## user.id

A unique identifier for a chat user, independent of their current connection — unlike clientId, it stays the same across sessions.

*↑ Back to [Owncast Documentation](Owncast-Documentation.md).*