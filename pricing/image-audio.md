---
title: Image, Audio and Real-Time Pricing
description: Per-image, per-character and per-second credit costs for Runway's image, audio, upscaling and real-time avatar models, with worked examples.
---

# Image, audio and real-time pricing

All figures are credits, at $0.01 per credit.

## Image generation

| Model | Pricing |
|---|---|
| `gen4_image` | 5 credits per 720p image, 8 per 1080p image |
| `gen4_image_turbo` | 2 credits per image, any resolution |
| `seedream5_lite` | 4 credits per image, any resolution |
| `seedream5_pro` | 5 credits per 1K image, 9 per 2K image |
| `gemini_2.5_flash` | 5 credits per image, any resolution |
| `grok_imagine_image_2` | 4–8 credits per image, by quality and resolution |
| `gemini_image3_pro` | 20 credits per 1K or 2K image, 40 per 4K image |
| `gpt_image_2` | 1–41 credits per image, by quality and resolution |

### Grok Imagine Image 2 tiers

Billed as `outputCount × perImage + referenceCount × 1`. Reference images cost 1 credit each and are charged **once per request**, not per output image.

| Quality | 1K (incl. `auto_1k`) | 2K (incl. `auto_2k`) |
|---|---|---|
| low | 4 | 6 |
| medium | 6 | 8 |

Default quality is medium, and anything other than `low` — including an omitted value — bills as medium. A plain 1K generation is 6 credits, which is also the minimum charge.

**Worked example:** four 1K medium images with two references is `4 × 6 + 2 = 26` credits. With `outputCount` capped at 4 and references at 3, the maximum single request is 35 credits.

### GPT Image 2 tiers

Billed as credits per image × `outputCount`. Default quality is **high**, and `auto` resolution bills at the 4K tier — so an unspecified request costs 41 credits per image.

| Quality | 1K / 2K | 4K (incl. `auto`) |
|---|---|---|
| low | 1 | 2 |
| medium | 5 | 11 |
| high | 20 | 41 |
| auto | 20 | 41 |

Set `quality` and resolution explicitly unless you intend to pay the high-4K rate.

## Image upscaling

| Model | Pricing |
|---|---|
| `magnific_precision_upscaler_v2` | 25 credits per image, or **150 credits** when output exceeds 4096px |

The sixfold jump above 4096px is the single largest cliff in image pricing. Check target dimensions before batching.

## Audio generation

| Model | Pricing |
|---|---|
| `seed_audio` | 0.25 credits per second (5 credit minimum) |
| `eleven_v3` | 1 credit per 50 characters (1 credit minimum) |
| `eleven_multilingual_v2` | 1 credit per 50 characters |
| `eleven_text_to_sound_v2` (duration given) | 1 credit per second of audio |
| `eleven_text_to_sound_v2` (no duration) | 2 credits |
| `eleven_voice_isolation` | 1 credit per 6s of audio |
| `eleven_voice_dubbing` | 1 credit per 2s of audio |
| `eleven_multilingual_sts_v2` | 1 credit per 3s of audio |

For short sound effects, omitting the duration on `eleven_text_to_sound_v2` costs a flat 2 credits — cheaper than any effect longer than two seconds.

## Real-time

| Model | Pricing |
|---|---|
| `gwm1_avatars` | 2 credits upfront, then 2 credits per 6 seconds |

A one-minute avatar conversation costs `2 + (60 / 6 × 2) = 22` credits — 22 cents. An hour is roughly 1,202 credits, about $12.

## Related

- [Image models](../models/image.md) — what each model produces
- [Audio models](../models/audio.md) — picking between the seven audio models
- [Pricing overview](./README.md) — credits, minimums and top-ups

<!-- widget:cta -->

## Two cents per image

Gen-4 Image Turbo is the cheapest way to see the API working.

[Create your account](https://dev.runwayml.com/) · [See the code](../get-started/using-the-api.md#generating-an-image)

<!-- /widget -->
