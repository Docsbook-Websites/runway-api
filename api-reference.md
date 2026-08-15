---
title: Runway API Reference — Endpoints and Parameters
description: Create generations, retrieve tasks and upload files. Every endpoint with its parameters, example requests and a playground you can call with your own key.
---

# API reference

The API is REST over HTTPS. Authenticate with your API key in the `Authorization` header as a bearer token; both SDKs read it from `RUNWAYML_API_SECRET` automatically.

All generation endpoints are asynchronous: they return a task ID, and you retrieve the result separately. See [waiting for output](./get-started/using-the-api.md#waiting-for-output).

<!-- widget:api -->

## POST /v1/image_to_video

Create a video generation task. Omit `promptImage` to generate from text alone on models that support it, such as `gen4.5`.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Model ID, e.g. `gen4.5`, `seedance2_5`, `gen4_turbo`, `aleph2` |
| `promptText` | string | no | Text description of the video to generate |
| `promptImage` | string | no | HTTPS URL, base64 data URI, or `runway://` URI for the starting image |
| `ratio` | string | no | Output dimensions, e.g. `1280:720`. Model-specific — see Inputs |
| `duration` | number | no | Output length in seconds. Model-specific ceiling |
| `seed` | number | no | Fixes the generation for reproducible output |

### Example

```bash
curl -X POST https://api.dev.runwayml.com/v1/image_to_video \
  -H "Authorization: Bearer $RUNWAYML_API_SECRET" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gen4.5",
    "promptText": "A serene mountain landscape at sunrise",
    "ratio": "1280:720",
    "duration": 5
  }'
```

### Response

```json
{
  "id": "17f20503-6c24-4c16-946b-35dbbce2af2f"
}
```

## POST /v1/text_to_image

Create an image generation task.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Model ID, e.g. `gen4_image`, `gen4_image_turbo`, `gpt_image_2` |
| `promptText` | string | yes | Text description of the image to generate |
| `ratio` | string | no | Output dimensions, e.g. `1360:768` |
| `referenceImages` | array | no | Reference images. Cost and count limits are model-specific |
| `outputCount` | number | no | Number of images to generate. Capped per model |
| `quality` | string | no | `low`, `medium`, `high` or `auto` on models with quality tiers |

### Example

```bash
curl -X POST https://api.dev.runwayml.com/v1/text_to_image \
  -H "Authorization: Bearer $RUNWAYML_API_SECRET" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gen4_image",
    "promptText": "A beautiful sunset over a calm ocean",
    "ratio": "1360:768"
  }'
```

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

### Statuses

| Status | Meaning |
|---|---|
| `PENDING` | Accepted and queued |
| `THROTTLED` | Stored but not enqueued — at your concurrency limit. Treat as `PENDING` |
| `RUNNING` | Generating |
| `SUCCEEDED` | Complete; `output` is populated |
| `FAILED` | Failed; read `failureCode` |
| `CANCELED` | Cancelled via the cancellation endpoint |

Output URLs expire within 24–48 hours — see [Outputs](./assets/outputs.md).

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

POST the file to `uploadUrl` as multipart form data, sending every key in `fields` as a form field and the contents as `file`. The `runwayUri` is then usable anywhere a URL is accepted, for 24 hours. If the upload fails, start over with a new `/v1/uploads` request rather than retrying.

## POST /v1/video_upscale

Upscale a video of at most 30 seconds.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Must be `magnific_video_upscaler_creative` |
| `videoUri` | string | yes | HTTPS URL, data URI or `runway://` URI of the source video |
| `resolution` | string | no | `720p`, `1k`, `2k` (default) or `4k` |
| `fpsBoost` | boolean | no | Increases output frame rate, which changes frame count and cost |

## POST /v1/character_performance

Drive a character's performance from a reference video using `act_two`.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Model ID, e.g. `act_two` |
| `character` | object | yes | The character to animate, as an image or video |
| `reference` | object | yes | The reference performance video |
| `ratio` | string | no | Output dimensions |

<!-- /widget -->

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

Full detail, including backoff strategy, is on the [HTTP errors](./errors/http-errors.md) page. Failures *after* a task is accepted are covered in [task failures](./errors/task-failures.md).

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

- [Making API calls](./get-started/using-the-api.md) — working code for every endpoint here
- [Models](./models/README.md) — valid `model` values
- [Inputs](./assets/inputs.md) — valid `ratio` values and asset constraints

<!-- widget:cta -->

## Call these endpoints with your own key

Set `RUNWAYML_API_SECRET` and the examples above run unmodified.

[Get an API key](https://dev.runwayml.com/) · [Read the quickstart](./get-started/using-the-api.md)

<!-- /widget -->
