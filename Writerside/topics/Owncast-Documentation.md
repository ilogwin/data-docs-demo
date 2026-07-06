# Owncast Documentation

[Owncast](https://github.com/owncast/owncast) is a self-hosted, open-source live streaming server. This section documents its architecture and its webhook system — a sample of documentation written for a real open-source project, covering two different genres: a system-level architecture overview and a detailed API reference.

This documentation focuses on two areas:
- The overall system architecture — how ingest, storage, backend, and frontend fit together.
- The webhook API used to integrate Owncast with external services.

## Contents

- **[Architecture Overview](Architecture-Overview.md)** — start here if you're new to the project. Explains how the system is structured end to end: ingest pipeline, backend request path, frontend, database schema, external connections. Six diagrams.
- **[Webhooks API Reference](Webhooks-Api-Reference.md)** — start here if you're building an integration. Concepts, authentication, endpoints, event types, payload formats, and working receiver code in Python and Node.js.

**Data objects referenced in webhook payloads:**

| Object | Events | Description |
|---|---|---|
| [User](User.md) | CHAT, USER_JOINED, NAME_CHANGE | A chat user |
| [Chat Message](Chat-Message.md) | CHAT | A single chat message |
| [Viewer Metrics](Viewer-Metrics.md) | STREAM_TITLE_UPDATED | Current, session-max, and overall-max viewer counts |

- [Glossary](Glossary.md) — key terms used across the webhook system (webhook, event type, eventData, clientId, user.id)

---

*Based on Owncast v0.2.5 · [github.com/owncast/owncast](https://github.com/owncast/owncast)*