# Owncast Documentation

[Owncast](https://github.com/owncast/owncast) is a self-hosted, open-source live streaming server. This section documents its architecture and its webhook system - a sample of documentation written for a real open-source project, covering two different genres: a system-level architecture overview and a detailed API reference.

## Where to start

**New to the project?** Start with the architecture overview - it explains how the system is structured end to end, from stream ingest to the frontend.

**Building an integration?** Go straight to the Webhooks API Reference - it's self-contained and includes everything needed to register a webhook and handle incoming events.

## Contents

### System design

- **[Architecture Overview](Architecture-Overview.md)** - how Owncast is structured: ingest pipeline, backend request path, frontend, database schema, and external connections. Six diagrams.
### Webhooks

- **[Webhooks API Reference](Webhooks-Api-Reference.md)** - concepts, authentication, endpoints, event types, payload formats, and working receiver code in Python and Node.js.
  **Data objects referenced in webhook payloads:**

| Object                               | Description                                                                               |
|--------------------------------------|-------------------------------------------------------------------------------------------|
| [User](User.md)                      | A chat user - appears inside CHAT, USER_JOINED, and NAME_CHANGE payloads                  |
| [Chat Message](Chat-Message.md)      | A single chat message - the payload of the CHAT event                                     |
| [Viewer Metrics](Viewer-Metrics.md) | Current, session-max, and overall-max viewer counts - carried inside STREAM_TITLE_UPDATED |

- **[Glossary](Glossary.md)** - key terms used across the webhook system (webhook, event type, eventData, clientId, user.id)
---

*Based on Owncast v0.2.x · [github.com/owncast/owncast](https://github.com/owncast/owncast)*