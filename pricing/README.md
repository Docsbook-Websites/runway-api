---
title: Runway API Pricing — Credits and Per-Model Costs
description: Credits cost $0.01 each with a $10 minimum. See what a second of video, an image or a minute of speech actually costs before you build.
---

# Pricing

Every generation costs credits. Credits cost **$0.01 each**, purchased in the developer portal for an organization, with a **$10 minimum** first purchase. Sales tax may apply depending on your location.

There is no subscription and no per-seat fee. You pay for generations.

<!-- widget:cards -->

- [Video pricing](./video.md) — Per-second rates for all 14 video models, plus upscaling {clapperboard}
- [Image and audio pricing](./image-audio.md) — Per-image and per-character rates {image}
- [Usage tiers](../usage/tiers.md) — Spend caps and how limits rise as you buy {gauge}
- [Autobilling](../usage/autobilling.md) — Automatic top-up so you don't run dry {refresh-cw}

<!-- /widget -->

## What things actually cost

| Generation | Model | Credits | Dollars |
|---|---|---|---|
| 5-second video | `gen4_turbo` | 25 | $0.25 |
| 5-second video | `gen4.5` | 60 | $0.60 |
| 10-second video | `gen4.5` | 120 | $1.20 |
| 30-second video at 720p | `seedance2_5` | 900 | $9.00 |
| 5-second video with audio | `veo3.1` | 200 | $2.00 |
| One 720p image | `gen4_image` | 5 | $0.05 |
| One image, cheapest | `gen4_image_turbo` | 2 | $0.02 |
| One 4K image | `gemini_image3_pro` | 40 | $0.40 |
| 500 characters of speech | `eleven_v3` | 10 | $0.10 |
| One minute of avatar conversation | `gwm1_avatars` | 22 | $0.22 |

The $10 minimum purchase therefore buys roughly 40 five-second Gen-4.5 clips, or 200 Gen-4 Image stills.

## Minimum charges

Several models charge a floor regardless of how short the generation is. Budget for these when generating many small assets:

| Model | Minimum per generation |
|---|---|
| `seedance2_5` | 80 credits |
| `seedance2_mini` | 64 credits |
| `aleph2` | 56 credits |
| `grok_imagine_image_2` | 6 credits |
| `seed_audio` | 5 credits |
| `eleven_v3` | 1 credit |
| `magnific_video_upscaler_creative` | 1 credit |

## Routed generations

Requests sent through a [model router](../models/routers.md) are billed at the standard rate of whichever model the router selects — there is no routing fee. The response metadata reports the model used and the realized credit cost, so read the actual figure from the response rather than estimating.

## Keeping the balance up

Running out of credits stops generation. [Autobilling](../usage/autobilling.md) checks your balance hourly and recharges when it falls below a threshold you set, with a minimum recharge of 1,000 credits ($10).

Each [usage tier](../usage/tiers.md) also caps how much you can spend in a rolling 30-day window — $100 at Tier 1, rising to $100,000 at Tier 5. Autobilling recharges are capped at your remaining monthly spend, and fail entirely if that remainder is under $10.

## Related

- [Video pricing](./video.md) — per-second rates in full
- [Image and audio pricing](./image-audio.md) — per-image and per-character rates
- [Models](../models/README.md) — what each model is for

<!-- widget:cta -->

## Start with $10

That is 1,000 credits — enough for roughly 40 five-second Gen-4.5 clips.

[Add credits](https://dev.runwayml.com/) · [Read the setup guide](../get-started/setup.md)

<!-- /widget -->
