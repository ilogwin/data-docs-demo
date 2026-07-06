# Viewer Metrics

Owncast exposes three related viewer-count metrics. All three are delivered
inside the `status` object of the `STREAM_TITLE_UPDATED` webhook event.

| Metric | Field | Type | Definition |
|---|---|---|---|
| Current viewer count | `viewerCount` | integer | The number of viewers currently connected to the live stream at a given moment. |
| Session max viewer count | `sessionMaxViewerCount` | integer | The highest number of concurrent viewers reached during the current streaming session. |
| Overall max viewer count | `overallMaxViewerCount` | integer | The highest number of concurrent viewers the server has ever reached, across all sessions. |

## Source event

`STREAM_TITLE_UPDATED` — all three fields arrive together inside the same
`status` object; there is no separate event per metric.

## See also

- [Webhooks API Reference](Webhooks-Api-Reference.md) — full payload format for STREAM_TITLE_UPDATED
- [Owncast Documentation](Owncast-Documentation.md) — back to overview