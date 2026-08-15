---
title: Runway API Reference — Endpoints and Parameters
description: Every Runway API endpoint with its parameters, example requests and responses — generation, task retrieval and uploads, plus the SDK method map.
---

# API reference

The API is REST over HTTPS. Authenticate with your API key in the `Authorization` header as a bearer token; both SDKs read it from `RUNWAYML_API_SECRET` automatically.

All generation endpoints are asynchronous: they return a task ID, and you retrieve the result separately.

<!-- widget:cards -->

- [Generation endpoints](./generation.md) — Video, image, character performance and upscaling {clapperboard}
- [Tasks and uploads](./tasks.md) — Retrieve results, upload large files, read task statuses {list-checks}

<!-- /widget -->

## Base URL and auth

```bash
curl -X POST https://api.dev.runwayml.com/v1/image_to_video \
  -H "Authorization: Bearer $RUNWAYML_API_SECRET" \
  -H "Content-Type: application/json" \
  -d '{ "model": "gen4.5", "promptText": "A sunrise", "ratio": "1280:720", "duration": 5 }'
```

## The request lifecycle

1. `POST` to a generation endpoint — returns `{ "id": "..." }` immediately.
2. `GET /v1/tasks/{id}` — poll until `SUCCEEDED`, `FAILED` or `CANCELED`.
3. Read `output[0]` and **download it within 24–48 hours** before the URL expires.

The SDKs collapse steps 1 and 2 into `.waitForTaskOutput()`. See [waiting for output](../get-started/using-the-api.md#waiting-for-output).

## Related

- [Making API calls](../get-started/using-the-api.md) — working code for every endpoint
- [Models](../models/README.md) — valid `model` values
- [HTTP errors](../errors/http-errors.md) — status codes and retry safety

<!-- widget:cta -->

## Call these endpoints with your own key

Set `RUNWAYML_API_SECRET` and every example here runs unmodified.

[Get an API key](https://dev.runwayml.com/) · [Read the quickstart](../get-started/using-the-api.md)

<!-- /widget -->
