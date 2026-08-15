---
title: Task and Upload Endpoints
description: Retrieve a task's status and output, and upload files up to 200MB with ephemeral uploads. Includes every task status and the SDK method map.
---

# Tasks and uploads

Generation endpoints return a task ID. These endpoints retrieve the result and move large files into place.

<!-- widget:api -->

## GET /v1/tasks/{id}

Retrieve a task's status and, once complete, its output.

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | yes | The task ID returned by a generation endpoint |

### Response

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

## POST /v1/uploads

Start an ephemeral upload for files up to 200MB.

| Field | Type | Required | Description |
|---|---|---|---|
| `filename` | string | yes | Filename with an extension representative of the contents |
| `type` | string | yes | Must be `ephemeral` |

### Response

```json
{
  "uploadUrl": "https://...",
  "fields": {},
  "runwayUri": "runway://..."
}
```

<!-- /widget -->

POST the file to `uploadUrl` as multipart form data, sending every key in `fields` as a form field and the contents as `file`. The `runwayUri` is then usable anywhere a URL is accepted, for 24 hours. If the upload fails, start over with a new `/v1/uploads` request rather than retrying — see [ephemeral uploads](../assets/uploads.md).

## Task statuses

| Status | Meaning |
|---|---|
| `PENDING` | Accepted and queued |
| `THROTTLED` | Stored but not enqueued — at your concurrency limit. Treat as `PENDING` |
| `RUNNING` | Generating |
| `SUCCEEDED` | Complete; `output` is populated |
| `FAILED` | Failed; read `failureCode` |
| `CANCELED` | Cancelled via the cancellation endpoint |

Output URLs expire within 24–48 hours — download and re-host them, as described in [Outputs](../assets/outputs.md).

## Error responses

| Status | Meaning | Retry? |
|---|---|---|
| `400` | Invalid input; `error` explains | No |
| `401` | Invalid API key | No |
| `404` | Resource not found | No |
| `405` | Method not supported on this endpoint | No |
| `429` | Rate or daily limit reached | Yes |
| `502` / `503` | Runway is shedding load | Yes |
| `504` | Runway is overloaded | Yes |

Full detail, including backoff strategy, is on the [HTTP errors](../errors/http-errors.md) page. Failures *after* a task is accepted are covered in [task failures](../errors/task-failures.md).

## SDK method map

| Operation | Endpoint | Node.js method |
|---|---|---|
| Generate an image | `POST /v1/text_to_image` | `client.textToImage.create` |
| Generate a video | `POST /v1/image_to_video` | `client.imageToVideo.create` |
| Character performance | `POST /v1/character_performance` | `client.characterPerformance.create` |
| Retrieve a task | `GET /v1/tasks/{id}` | `client.tasks.retrieve` |
| Upload a file | `POST /v1/uploads` | `client.uploads.createEphemeral` |

**Node.js** — [`@runwayml/sdk`](https://www.npmjs.com/package/@runwayml/sdk), TypeScript bindings, Node 18+.
**Python** — [`runwayml`](https://pypi.org/project/runwayml/), MyPy-compatible annotations, Python 3.8+.

Both SDKs retry retryable statuses automatically and expose `waitForTaskOutput` for correct polling.

## Related

- [Generation endpoints](./generation.md) — creating the tasks you retrieve here
- [Making API calls](../get-started/using-the-api.md) — working code for all of it
- [Ephemeral uploads](../assets/uploads.md) — the SDK path for large files

<!-- widget:cta -->

## Build against the real endpoints

A $10 credit purchase unlocks every endpoint on this page.

[Get an API key](https://dev.runwayml.com/) · [See pricing](../pricing/README.md)

<!-- /widget -->
