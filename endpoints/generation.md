---
title: Generation Endpoints — Video, Image and Character
description: Create video, image and character-performance tasks. Every parameter, example request and response, with a playground you can call with your own key.
---

# Generation endpoints

Every generation endpoint is asynchronous: it returns a task ID immediately, and you retrieve the result separately. See [waiting for output](../get-started/using-the-api.md#waiting-for-output).

Authenticate with your API key as a bearer token in the `Authorization` header. Both SDKs read it from `RUNWAYML_API_SECRET` automatically.

<!-- widget:api -->

## POST /v1/image_to_video

Create a video generation task. Omit `promptImage` to generate from text alone on models that support it, such as `gen4.5`.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Model ID, e.g. `gen4.5`, `seedance2_5`, `gen4_turbo`, `aleph2` |
| `promptText` | string | no | Text description of the video to generate |
| `promptImage` | string | no | HTTPS URL, base64 data URI, or `runway://` URI for the starting image |
| `ratio` | string | no | Output dimensions, e.g. `1280:720`. Model-specific |
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

## POST /v1/character_performance

Drive a character's performance from a reference video using `act_two`.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Model ID, e.g. `act_two` |
| `character` | object | yes | The character to animate, as an image or video |
| `reference` | object | yes | The reference performance video |
| `ratio` | string | no | Output dimensions |

## POST /v1/video_upscale

Upscale a video of at most 30 seconds.

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | yes | Must be `magnific_video_upscaler_creative` |
| `videoUri` | string | yes | HTTPS URL, data URI or `runway://` URI of the source video |
| `resolution` | string | no | `720p`, `1k`, `2k` (default) or `4k` |
| `fpsBoost` | boolean | no | Increases output frame rate, which changes frame count and cost |

<!-- /widget -->

## Related

- [Tasks and uploads](./tasks.md) — retrieving results and uploading files
- [Models](../models/README.md) — valid `model` values
- [Inputs](../assets/inputs.md) — valid `ratio` values and asset constraints

<!-- widget:cta -->

## Call these endpoints with your own key

Set `RUNWAYML_API_SECRET` and the examples above run unmodified.

[Get an API key](https://dev.runwayml.com/) · [Read the quickstart](../get-started/using-the-api.md)

<!-- /widget -->
