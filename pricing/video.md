---
title: Video Generation Pricing — Cost per Second by Model
description: Exact credit cost per second for every Runway video model at each resolution, including reference-media charges, minimums and upscaling formulas.
---

# Video pricing

All figures are credits, at $0.01 per credit.

## Per-second rates

| Model | Pricing |
|---|---|
| `seedance2_5` (720p) | 30 credits per second of output, plus 15 per second of input and reference video (combined video capped at 30 seconds); reference images and audio free. 80 credit minimum. |
| `seedance2_5` (480p) | 20 credits per second of output, plus 10 per second of input and reference video (combined video capped at 30 seconds); reference images and audio free. 80 credit minimum. |
| `seedance2` (480p/720p) | 36 credits per second |
| `seedance2` (1080p) | 40 credits per second |
| `seedance2` (4K) | 150 credits per second |
| `seedance2_fast` (480p/720p) | 29 credits per second |
| `seedance2_mini` (480p/720p) | 16 credits per second. 64 credit minimum. |
| `gen4.5` | 12 credits per second |
| `gen4_turbo` | 5 credits per second |
| `act_two` | 5 credits per second |
| `aleph2` | 28 credits per second. 56 credit minimum. |
| `veo3.1` (audio) | 40 credits per second |
| `veo3.1` (no audio) | 20 credits per second |
| `veo3.1_fast` (audio) | 15 credits per second |
| `veo3.1_fast` (no audio) | 10 credits per second |
| `hailuo3` (768P) | 10 credits per second, plus 2 per reference image; reference video billed at the output rate, capped at 15 seconds |
| `hailuo3` (2K) | 15 credits per second, plus 2 per reference image; reference video billed at the output rate, capped at 15 seconds |
| `grok_imagine_1_5` (480p) | 10 credits per second, plus 1 per image or audio reference (including an image-to-video start frame) |
| `grok_imagine_1_5` (720p) | 16 credits per second, plus 1 per image or audio reference |
| `grok_imagine_1_5` (1080p) | 29 credits per second, plus 1 per image or audio reference |
| `happyhorse_1_0` (720p) | 15 credits per second |
| `happyhorse_1_0` (1080p) | 30 credits per second |
| `gemini_omni_flash` (text to video) | 10 credits per second |
| `gemini_omni_flash` (image to video) | 10 credits per second, plus 1 for the first-frame image |
| `gemini_omni_flash` (video to video) | 11 credits per second of input video (capped at 10 seconds), plus 1 per reference image |

## What reference media costs

The per-second rate is not the whole bill on models that accept references:

- **Seedance 2.5** charges half the output rate for every second of input and reference *video*, but reference images and audio are free. A 10-second 720p generation with 6 seconds of reference video costs `10 × 30 + 6 × 15 = 390` credits.
- **Hailuo 3.0** bills reference video at the same rate as output, and 2 credits per reference image.
- **Grok Imagine Video 1.5** charges 1 credit per image or audio reference, including the start frame in image-to-video mode.
- **Gemini Omni Flash** charges 1 credit for the first-frame image.

## Video upscaling

`magnific_video_upscaler_creative` bills per output **frame**, not per second:

```
USD     = rate × fps × duration_seconds
credits = ceil(USD / 0.01)
```

Minimum 1 credit per generation.

| Resolution | USD per frame | Example: 10s @ 30fps |
|---|---|---|
| 720p, 1k | $0.007 | 210 credits |
| 2k | $0.009 | 270 credits |
| 4k | $0.012 | 360 credits |

When `fpsBoost` is enabled the output frame count may differ from the input, which changes the cost. Compute against the *output* frame count.

## Recipe pricing

Recipes are pre-built multi-step pipelines. They cost more than a raw generation because they run several.

| Recipe | Pricing |
|---|---|
| `product_ad` | 720p: 200 credits for 4 seconds, +36 per additional second. 1080p: 216 credits for 4 seconds, +40 per additional second. 4–15 seconds. |
| `product_swap` | 720p: 212 credits for 4 seconds, +36 per additional second. 1080p: 228 credits for 4 seconds, +40 per additional second. 4–15 seconds. |
| `product_ugc` | 720p: 192 credits for 4 seconds, +36 per additional second. 1080p: 208 credits for 4 seconds, +40 per additional second. 4–15 seconds. |
| `multi_shot_video` | 720p: 13 credits per second. 1080p: 17 credits per second. 5–15 seconds. |
| `ad_localization` | 21 credits per localized image |
| `marketing_stock_image` | 8 credits per call for prompt processing, plus 1–20 credits per generated image by quality × `outputCount`. Default (4 images, high quality): 88 credits. |
| `product_campaign_image` | 36 credits per 1080p image, 4 images generated per call |

## Related

- [Video models](../models/video.md) — what each model does and its limits
- [Pricing overview](./README.md) — credits, minimums and worked examples
- [Usage tiers](../usage/tiers.md) — monthly spend caps by tier

<!-- widget:cta -->

## Model your costs before you build

The playground shows realized credit cost on every run.

[Open the playground](https://dev.runwayml.com/models) · [Add credits](https://dev.runwayml.com/)

<!-- /widget -->
