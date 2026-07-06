# Webhooks API Reference

This document covers Owncast's webhook system, which lets external applications react to server events (chat messages, stream start/stop, and more) in real time.

## What are webhooks

Webhooks are the opposite of polling. Instead of your integration asking Owncast "did anything happen?" on a schedule, Owncast calls your server the moment something happens.

You register a URL. Owncast sends an HTTP POST to that URL whenever a specific event occurs — a stream goes live, a viewer sends a chat message, a user joins. Your server receives the payload and acts on it.

```
Without webhooks (polling):
Your app --GET /status--> Owncast   (every 5 seconds, most calls return nothing new)
 
With webhooks (push):
Owncast --POST /your-endpoint--> Your app   (only when something actually happens)
```

**When to use webhooks instead of the REST API:**
- You need to react to events in real time (stream started, chat message received)
- You want to build a Discord bot that announces when the stream goes live
- You need to log all chat messages to an external database
- You want to trigger other services when a specific event occurs
  **When to use the REST API instead:**
- You need to query current state (how many viewers right now?)
- You want to send something to Owncast (post a message, change the title)
- You need historical data
---

## Authentication

Webhook management endpoints are part of the Admin API. They use HTTP Basic Auth with the admin password — not Bearer tokens.

```
Authorization: Basic <base64(admin:<your-admin-password>)>
```

**Example — encoding credentials:**

```bash
# Admin password: mypassword
echo -n "admin:mypassword" | base64
# Output: YWRtaW46bXlwYXNzd29yZA==
 
# Use in header:
# Authorization: Basic YWRtaW46bXlwYXNzd29yZA==
```

> Never expose your admin credentials in client-side code or public repositories. Webhook management should only be done from a secure backend environment.
>
{style="warning"}
 
---

## Endpoints

### List all webhooks

Returns all currently registered webhooks.

```
GET /api/admin/webhooks
```

**Request**

```bash
curl -X GET http://your-server:8080/api/admin/webhooks \
  -H "Authorization: Basic YWRtaW46bXlwYXNzd29yZA=="
```

**Response — 200 OK**

```json
[
  {
    "id": 1,
    "url": "https://your-app.example.com/owncast/events",
    "events": ["STREAM_STARTED", "STREAM_STOPPED", "CHAT"]
  },
  {
    "id": 2,
    "url": "https://discord-relay.example.com/hook",
    "events": ["STREAM_STARTED"]
  }
]
```

**Response fields**

| Field | Type | Description |
|---|---|---|
| `id` | integer | Unique webhook ID. Required for deletion. |
| `url` | string | The URL Owncast sends POST requests to when events occur |
| `events` | array of strings | List of event types this webhook is subscribed to |

Returns an empty array `[]` if no webhooks are registered.
 
---

### Create a webhook

Registers a new webhook URL and subscribes it to one or more event types.

```
POST /api/admin/webhooks/create
```

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `url` | string | yes | The HTTPS endpoint that will receive event payloads |
| `events` | array of strings | yes | One or more event types to subscribe to (see event types below) |

```json
{
  "url": "https://your-app.example.com/owncast/events",
  "events": ["STREAM_STARTED", "STREAM_STOPPED", "CHAT"]
}
```

**Request**

```bash
curl -X POST http://your-server:8080/api/admin/webhooks/create \
  -H "Authorization: Basic YWRtaW46bXlwYXNzd29yZA==" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.example.com/owncast/events",
    "events": ["STREAM_STARTED", "STREAM_STOPPED", "CHAT"]
  }'
```

**Response — 200 OK**

```json
{
  "id": 3,
  "url": "https://your-app.example.com/owncast/events",
  "events": ["STREAM_STARTED", "STREAM_STOPPED", "CHAT"]
}
```

The response contains the newly created webhook object, including the `id` assigned by the server. Store this `id` if you need to delete the webhook later.

**Response — 400 Bad Request**

```json
{
  "success": false,
  "message": "unable to create webhook"
}
```

Common causes: missing `url` field, invalid URL format, empty `events` array.
 
---

### Delete a webhook

Removes a registered webhook. Owncast will stop sending events to its URL immediately.

```
POST /api/admin/webhooks/delete
```

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | The ID of the webhook to delete (from list or create response) |

```json
{
  "id": 3
}
```

**Request**

```bash
curl -X POST http://your-server:8080/api/admin/webhooks/delete \
  -H "Authorization: Basic YWRtaW46bXlwYXNzd29yZA==" \
  -H "Content-Type: application/json" \
  -d '{"id": 3}'
```

**Response — 200 OK**

```json
{
  "success": true,
  "message": ""
}
```

**Response — 400 Bad Request**

```json
{
  "success": false,
  "message": "unable to delete webhook"
}
```

Common cause: webhook with the given `id` does not exist.
 
---

## Event types

These are the values you can include in the `events` array when creating a webhook.

| Event type | When it fires |
|---|---|
| `STREAM_STARTED` | The broadcaster connects and a stream begins |
| `STREAM_STOPPED` | The stream ends (broadcaster disconnects) |
| `CHAT` | A viewer sends a chat message |
| `NAME_CHANGE` | A viewer changes their display name |
| `USER_JOINED` | A new viewer connects to the chat |
| `VISIBILITY-UPDATE` | A chat message is hidden or restored |
| `STREAM_TITLE_UPDATED` | The stream title is changed |
| `CONNECTED_CHAT_CLIENT_QUALITY_CHANGE` | A viewer's connection quality changes |

Subscribe only to the events your integration actually needs. Subscribing to `CHAT` on a busy stream sends one POST request per message — plan your endpoint's throughput accordingly.
 
---

## Incoming payload format

When a subscribed event occurs, Owncast sends a POST request to your registered URL with a JSON body. The top-level structure is the same for all event types; the `body` field contains the event-specific data.

### Common envelope

```json
{
  "type": "STREAM_STARTED",
  "eventData": {
    "serverTime": "2026-05-20T14:00:00Z"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `type` | string | The event type that fired (matches values in your `events` list) |
| [`eventData`](Glossary.md#eventdata) | object | Event-specific payload (structure varies by type — see below) |

### STREAM_STARTED and STREAM_STOPPED

```json
{
  "type": "STREAM_STARTED",
  "eventData": {
    "serverTime": "2026-05-20T14:00:00Z"
  }
}
```

### CHAT

```json
{
  "type": "CHAT",
  "eventData": {
    "id": "msg-abc123",
    "timestamp": "2026-05-20T14:32:00Z",
    "body": "Hello everyone!",
    "user": {
      "id": "user-xyz",
      "displayName": "StreamFan42",
      "displayColor": 7,
      "isBot": false,
      "authenticated": false,
      "scopes": []
    }
  }
}
```

### NAME_CHANGE

```json
{
  "type": "NAME_CHANGE",
  "eventData": {
    "user": {
      "id": "user-xyz",
      "displayName": "NewName99",
      "previousNames": ["StreamFan42"]
    }
  }
}
```
 
---

## Implementing a receiver

Your webhook endpoint must:

1. Accept `POST` requests with `Content-Type: application/json`
2. Read and parse the JSON body
3. Return HTTP `200` within a reasonable timeout (Owncast does not retry on failure)
4. Handle events asynchronously — do not block the response while processing
   **Minimal example in Python (Flask):**

```python
from flask import Flask, request, jsonify
 
app = Flask(__name__)
 
@app.route("/owncast/events", methods=["POST"])
def receive_event():
    payload = request.get_json()
    event_type = payload.get("type")
    event_data = payload.get("eventData", {})
 
    if event_type == "STREAM_STARTED":
        print("Stream went live!")
        # notify_discord("Stream is live!")
 
    elif event_type == "CHAT":
        user = event_data.get("user", {})
        body = event_data.get("body", "")
        print(f"{user['displayName']}: {body}")
        # check_for_commands(user, body)
 
    elif event_type == "STREAM_STOPPED":
        print("Stream ended.")
 
    # Always return 200 immediately
    return jsonify({"ok": True}), 200
 
if __name__ == "__main__":
    app.run(port=5000)
```

**Minimal example in Node.js (Express):**

```javascript
const express = require("express");
const app = express();
app.use(express.json());
 
app.post("/owncast/events", (req, res) => {
  const { type, eventData } = req.body;
 
  switch (type) {
    case "STREAM_STARTED":
      console.log("Stream went live!");
      break;
    case "CHAT":
      console.log(`${eventData.user.displayName}: ${eventData.body}`);
      break;
    case "STREAM_STOPPED":
      console.log("Stream ended.");
      break;
  }
 
  // Always respond 200 immediately
  res.status(200).json({ ok: true });
});
 
app.listen(5000, () => console.log("Webhook receiver running on port 5000"));
```
 
---

## Complete workflow example

**Goal:** Create a webhook that notifies a Discord channel when a stream starts.

**Step 1.** Set up your receiver and make it publicly accessible (e.g. using a server or a tunnel like ngrok during development).

**Step 2.** Register the webhook:

```bash
curl -X POST http://your-owncast-server:8080/api/admin/webhooks/create \
  -H "Authorization: Basic YWRtaW46bXlwYXNzd29yZA==" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-receiver.example.com/owncast/events",
    "events": ["STREAM_STARTED", "STREAM_STOPPED"]
  }'
```

Save the returned `id`.

**Step 3.** Start a test stream and verify your receiver logs the `STREAM_STARTED` event.

**Step 4.** When you no longer need the webhook:

```bash
curl -X POST http://your-owncast-server:8080/api/admin/webhooks/delete \
  -H "Authorization: Basic YWRtaW46bXlwYXNzd29yZA==" \
  -H "Content-Type: application/json" \
  -d '{"id": 3}'
```
 
---

## Error handling reference

| HTTP status | Meaning | Action |
|---|---|---|
| `200` | Success | — |
| `400` | Bad request | Check request body: missing fields, invalid URL, unknown event type |
| `401` | Unauthorized | Check Basic Auth credentials; admin password may have changed |
| `500` | Server error | Check Owncast server logs |

Owncast does **not** retry failed webhook deliveries. If your endpoint is down when an event fires, that event is lost. Design your receiver to be reliable, or poll the REST API as a fallback for critical events.
 
---

## Related resources

- [Architecture Overview](Architecture-Overview.md) — how the `webhooks/` service fits into the overall system
- [User](User.md) — structure of the `user` object referenced in CHAT and NAME_CHANGE payloads
- [Chat Message](Chat-Message.md) — full structure of the CHAT payload's message data
- [Viewer Metrics](Viewer-Metrics.md) — fields carried inside the STREAM_TITLE_UPDATED payload
- [Full API reference](https://owncast.online/api/latest/#tag/Notifications) — Notifications section
- [Building integrations](https://owncast.online/thirdparty/) — third-party tools built on Owncast
- [Access tokens](https://owncast.online/docs/api/) — for REST API integrations that don't need webhooks
- [Owncast Documentation](Owncast-Documentation.md) — back to overview
---

*Based on Owncast v0.2.4 · [github.com/owncast/owncast](https://github.com/owncast/owncast)*