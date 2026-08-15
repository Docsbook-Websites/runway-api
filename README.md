---
title: Runway API — Generative Video, Image and Audio Models
description: Bring Runway's generative video, image and audio models into your product with one API. Start with a five-line call, ship to production on the same key.
---

# Runway API

The Runway API puts our generative models — video, image, audio and real-time avatars — behind a single HTTP interface and two typed SDKs. You create a task, you poll it or await it, you get back a URL.

The same API generates millions of videos a month for some of the world's largest consumer technology companies. It costs $10 to start.

<!-- widget:cta -->

**Two minutes to first frame**

## Generate your first video today

Create an organization, add credits, and run the quickstart below — no sales call, no waitlist.

[Create your account](https://dev.runwayml.com/) · [See pricing](./pricing/README.md)

<!-- /widget -->

## Start here

<!-- widget:cards -->

### First steps

- [Set up your account](./get-started/setup.md) — Create an organization, mint a key, add credits {key-round}
- [Make your first call](./get-started/using-the-api.md) — Text-to-video and image-to-video in one request {rocket}
- [Go-live checklist](./get-started/go-live.md) — What to fix before real users hit your integration {clipboard-check}

### Reference

- [Models](./models/README.md) — 30+ video, image and audio models and what each is for {boxes}
- [Pricing](./pricing/README.md) — Exact credit cost of every model, per second and per image {credit-card}
- [API reference](./api/README.md) — Endpoints, parameters and a live playground {code}

### Building for production

- [Inputs](./assets/inputs.md) — Size limits, codecs, aspect ratios, URL requirements {file-input}
- [Errors and retries](./errors/http-errors.md) — Which failures are safe to retry, and how {triangle-alert}
- [Usage tiers](./usage/tiers.md) — Concurrency and daily limits, and how to raise them {gauge}

<!-- /widget -->

## Quickstart

Install the SDK:

```bash
npm install --save @runwayml/sdk
```

Export your key, then generate a five-second video from a text prompt with Gen-4.5:

```js
import RunwayML, { TaskFailedError } from '@runwayml/sdk';

const client = new RunwayML();

try {
  const task = await client.imageToVideo
    .create({
      model: 'gen4.5',
      promptText: 'A serene mountain landscape at sunrise with mist rolling through the valleys',
      ratio: '1280:720',
      duration: 5,
    })
    .waitForTaskOutput();

  console.log('Video URL:', task.output[0]);
} catch (error) {
  if (error instanceof TaskFailedError) {
    console.error('The video failed to generate.', error.taskDetails);
  } else {
    throw error;
  }
}
```

That call costs 60 credits — 12 credits per second of Gen-4.5 output, or $0.60. See [pricing](./pricing/README.md) for every model's rate.

## What you can generate

| You want | Use | Starting cost |
|---|---|---|
| Cinematic video up to 30 seconds | `seedance2_5` | 20 credits/second at 480p |
| Best-in-class motion and prompt adherence | `gen4.5` | 12 credits/second |
| The cheapest usable video | `gen4_turbo` | 5 credits/second |
| Edit an existing video with a prompt | `aleph2` | 28 credits/second |
| A production-grade still image | `gen4_image` | 5 credits per 720p image |
| A real-time conversational avatar | `gwm1_avatars` | 2 credits per 6 seconds |
| Speech or sound effects | `seed_audio` | 0.25 credits/second |

Not sure which model to pick? A [model router](./models/routers.md) chooses one per request based on your cost, latency or quality preference — at no extra charge.

## How the API works

Every generation is a **task**. You create it, and it runs asynchronously:

1. `POST /v1/image_to_video` (or any generation endpoint) returns a task `id` immediately.
2. The task moves through `PENDING` → `RUNNING` → `SUCCEEDED`, `FAILED` or `CANCELED`.
3. `GET /v1/tasks/{id}` returns the current status, and on success an `output` array of URLs.

The SDKs collapse steps 2 and 3 into `.waitForTaskOutput()`, which polls correctly for you — with backoff, jitter and a timeout. See [SDKs and task polling](./get-started/using-the-api.md#waiting-for-output) for when to use it and when to store the task ID instead.

Output URLs are ephemeral and expire 24–48 hours after the task completes. Download and re-host anything you show to users — see [Outputs](./assets/outputs.md).

## Scale

Every organization starts at Tier 1: one concurrent generation, 50 per day. Tiers rise automatically as you purchase credits, with no waiting period — $100 purchased puts you on Tier 3 (5 concurrent, 1,000/day) the moment it clears.

Beyond Tier 5 there are enterprise partnerships: guaranteed concurrency, Slack support, earliest access to new models, and custom payment terms. File an exception request from the usage page of the developer portal, or contact enterprise@runwayml.com.

See [usage tiers](./usage/tiers.md) for the full table.

<!-- widget:cta -->

## Ready to build?

A $10 minimum purchase unlocks the API — the same endpoints that serve production traffic at consumer scale.

[Create your account](https://dev.runwayml.com/) · [Read the setup guide](./get-started/setup.md)

<!-- /widget -->
