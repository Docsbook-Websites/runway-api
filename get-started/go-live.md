---
title: Production Launch Checklist for the Runway API
description: Raise your limits, handle the failures that actually happen, lock down your API key and monitor the right metrics before real users reach your integration.
---

# Go-live checklist

The failures that hurt in production are rarely the ones you tested for. Work through these four areas before launch.

<!-- widget:accordion -->

### 1. Manage your usage

**Tier up before launch, not during it.** Your organization's concurrency and daily generation limits are set by its [usage tier](../usage/tiers.md). If you have only been testing, you are probably on Tier 1 — one concurrent generation, 50 per day. That will not survive a launch.

Tiers rise automatically with credits purchased, with no waiting period: $100 puts you on Tier 3 (5 concurrent, 1,000/day), $1,000 on Tier 4 (10 concurrent, 5,000/day). Estimate your peak daily generations and buy ahead of it.

**Set up autobilling.** Running out of credits mid-launch takes your feature offline. [Autobilling](../usage/autobilling.md) recharges automatically when your balance falls below a threshold you choose. Note that if your remaining monthly spend is below $10, autobilling fails — so watch your tier's monthly spend cap too.

### 2. Test your integration

**Test the failures, not just the happy path.** At minimum, your integration should survive:

- `429 Too Many Requests` — you hit a rate or daily limit. Back off and retry.
- `503 Service Unavailable` — Runway is shedding load. Retry with exponential backoff and jitter.
- `400 Bad Request` — your input is invalid. Never retry; fix the input.

The full matrix, with which codes are safe to retry, is on the [HTTP errors](../errors/http-errors.md) page.

**Validate inputs before sending them.** An oversized image or an unsupported codec in `promptImage` returns `400`. Test with a spread of real user inputs — odd aspect ratios, huge files, unusual containers — and check them against the [inputs reference](../assets/inputs.md). Every URL you pass must be HTTPS, resolve to a domain rather than an IP, answer HTTP HEAD, and return a correct `Content-Type`.

### 3. Secure your integration

**Never hard-code the key.** Load it from a secret manager or securely-set environment variables. Recommended stores: HashiCorp Vault, AWS Secrets Manager, Google Cloud Secret Manager, Azure Key Vault, Render environment variables, Heroku config vars.

Check your repository history for keys that are already committed:

```bash
git grep "key_"
```

If you find one — or find a key that was ever stored insecurely — disable it immediately from the API Keys tab and mint a replacement.

**Stop sharing keys.** Create keys liberally and revoke them when they are done. Staging gets its own key, separate from production. Every developer testing locally gets their own. Any key shared between people or environments should be disabled and replaced, because a shared key cannot be revoked without breaking everyone.

Remember that keys are org-scoped: removing a person from the organization does not revoke the keys they created.

### 4. Monitor your integration

Three metrics tell you almost everything:

- **API error rate.** Some errors are normal — a `404` on an idempotent delete, for example. A rising `429` rate means you are being throttled at a limit.
- **API request count per day.** This is your credit burn and your distance from the tier's daily cap.
- **Throttled task count.** A `THROTTLED` task is safe to treat as `PENDING` — it is stored but not yet enqueued. A lot of them means you are pressed against your concurrency limit and should tier up.

**Make sure you receive Runway's emails.** Autobilling charges and payment failures are announced by email to the address that registered the developer portal account. If those go to an unmonitored inbox or a spam folder, the first sign of a failed recharge will be an outage.

**Avoid account suspension.** Runway moderates unsafe requests, and repeated moderated requests lead to suspension. Confirm your use case is not in a moderated category, and add your own [content moderation](../errors/task-failures.md#safety-failures) upstream if user-supplied prompts reach the API.

<!-- /widget -->

## Quick pre-flight table

| Check | Where |
|---|---|
| Tier supports peak daily generations | [Usage tiers](../usage/tiers.md) |
| Autobilling configured and funded | [Autobilling](../usage/autobilling.md) |
| `429` / `503` handled with backoff + jitter | [HTTP errors](../errors/http-errors.md) |
| `failureCode` branching implemented | [Task failures](../errors/task-failures.md) |
| Inputs validated against size and codec limits | [Inputs](../assets/inputs.md) |
| Output URLs downloaded and re-hosted | [Outputs](../assets/outputs.md) |
| Key in a secret store, not in the repo | Section 3 above |
| Staging and production keys separated | Section 3 above |

## Endpoint reference

Every endpoint your integration will call, with its parameters.

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

### SDK method map

| Operation | Endpoint | Node.js method |
|---|---|---|
| Generate an image | `POST /v1/text_to_image` | `client.textToImage.create` |
| Generate a video | `POST /v1/image_to_video` | `client.imageToVideo.create` |
| Character performance | `POST /v1/character_performance` | `client.characterPerformance.create` |
| Retrieve a task | `GET /v1/tasks/{id}` | `client.tasks.retrieve` |
| Upload a file | `POST /v1/uploads` | `client.uploads.createEphemeral` |

**Node.js** — [`@runwayml/sdk`](https://www.npmjs.com/package/@runwayml/sdk), TypeScript bindings, Node 18+.
**Python** — [`runwayml`](https://pypi.org/project/runwayml/), MyPy-compatible annotations, Python 3.8+.

## Related

- [Making API calls](./using-the-api.md) — the code this checklist protects
- [Usage tiers](../usage/tiers.md) — concurrency, daily and monthly limits
- [Task failures](../errors/task-failures.md) — which failures refund credits

<!-- widget:cta -->

## Need higher limits than Tier 5?

Guaranteed concurrency, Slack support and custom terms come with an enterprise partnership.

[File an exception request](https://dev.runwayml.com/) · [Read the tier table](../usage/tiers.md)

<!-- /widget -->
