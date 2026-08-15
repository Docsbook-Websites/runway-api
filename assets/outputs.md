---
title: API Outputs — Result URLs and Their Expiry
description: Successful tasks return an array of output URLs that expire within 24 to 48 hours. Download and re-host them — never expose Runway URLs in your product.
---

# Outputs

When a task succeeds, `GET /v1/tasks/{id}` returns:

```json
{
  "id": "d2e3d1f4-1b3c-4b5c-8d46-1c1d7ee86892",
  "status": "SUCCEEDED",
  "createdAt": "2024-06-27T19:49:32.335Z",
  "output": [
    "https://dnznrvs05pmza.cloudfront.net/output.mp4?_jwt=..."
  ]
}
```

The `output` array holds one or more URLs pointing at the result.

## Output URLs expire

These URLs are **ephemeral**: they expire within **24 to 48 hours** of accessing the API.

Download the data and save it to your own storage. **Do not expose these URLs directly in your product** — anything you persist that points at them (a database row, a cached page, an email, a social post) breaks within two days.

The correct shape is:

1. Task succeeds; read `output[0]`.
2. Download the asset server-side, immediately.
3. Store it in your own object storage or CDN.
4. Serve your own URL to users.

Running step 2 as a background job is fine, provided it runs well inside the expiry window and retries on failure.

## Reading the output in code

Using `waitForTaskOutput`, the resolved task carries the same `output` array:

```js
const task = await client.imageToVideo
  .create({
    model: 'gen4.5',
    promptText: 'A serene mountain landscape at sunrise',
    ratio: '1280:720',
    duration: 5,
  })
  .waitForTaskOutput();

const videoUrl = task.output[0];
// Download videoUrl and re-host it before showing it to a user.
```

## Task statuses

A task passes through these states:

| Status | Meaning |
|---|---|
| `PENDING` | Accepted and queued |
| `THROTTLED` | Stored but not yet enqueued — you are at your concurrency limit. Treat as `PENDING`. |
| `RUNNING` | Generating |
| `SUCCEEDED` | Complete; `output` is populated |
| `FAILED` | Failed; check `failureCode` |
| `CANCELED` | Cancelled via the cancellation endpoint |

A steady stream of `THROTTLED` tasks means you are pressed against your [tier's concurrency limit](../usage/tiers.md).

## Related

- [Task failures](../errors/task-failures.md) — what `failureCode` values mean
- [Making API calls](../get-started/using-the-api.md#waiting-for-output) — polling correctly
- [Usage tiers](../usage/tiers.md) — why tasks get throttled

<!-- widget:cta -->

## Build the download step in from day one

Retrofitting re-hosting after URLs start expiring is the expensive path.

[Read the go-live checklist](../get-started/go-live.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
