---
title: Image Generation and Upscaling Models
description: Nine image models from 2 to 41 credits per image, plus precision upscaling. Compare quality tiers, resolutions and reference-image behaviour.
---

# Image models

Nine models generate stills, all of them accepting text prompts and reference images. They differ mainly in quality tiers and what a 4K output costs.

## At a glance

| Model | Resolutions | Cost |
|---|---|---|
| `gen4_image_turbo` | Any | 2 credits per image |
| `seedream5_lite` | Any | 4 credits per image |
| `gen4_image` | 720p, 1080p | 5 / 8 credits |
| `seedream5_pro` | 1K, 2K | 5 / 9 credits |
| `gemini_2.5_flash` | Any | 5 credits per image |
| `grok_imagine_image_2` | 1K, 2K | 4–8 credits |
| `gpt_image_2` | 1K, 2K, 4K | 1–41 credits |
| `gemini_image3_pro` | 1K, 2K, 4K | 20 / 40 credits |
| `gemini_image3.1_flash` | — | — |

<!-- widget:accordion -->

### Gen-4 Image and Gen-4 Image Turbo

`gen4_image` produces production-grade stills at 5 credits per 720p image or 8 per 1080p image. `gen4_image_turbo` produces images at any resolution for a flat **2 credits**, the cheapest option in the catalogue.

Gen-4 Image Turbo requires both text and image references; Gen-4 Image accepts either.

```js
const imageTask = await client.textToImage
  .create({
    model: 'gen4_image',
    promptText: 'A beautiful sunset over a calm ocean',
    ratio: '1360:768',
  })
  .waitForTaskOutput();
```

### Grok Imagine Image 2 — how the reference maths works

`grok_imagine_image_2` bills as `outputCount × perImage + referenceCount × 1`. Reference images cost 1 credit each and are charged **once per request, not per output image**.

| Quality | 1K (incl. `auto_1k`) | 2K (incl. `auto_2k`) |
|---|---|---|
| low | 4 | 6 |
| medium | 6 | 8 |

Default quality is medium, and anything other than `low` — including an omitted value — bills as medium. A plain 1K generation is 6 credits, which is also the minimum charge.

Four 1K medium images with two references is `4 × 6 + 2 = 26` credits. With `outputCount` capped at 4 and references at 3, the maximum for a single request is 35 credits.

### GPT Image 2 — widest quality range

`gpt_image_2` bills as credits per image × `outputCount`. Default quality is **high**, and the `auto` resolution bills at the 4K tier — which means an unspecified request costs 41 credits per image, not 1.

| Quality | 1K / 2K | 4K (incl. `auto`) |
|---|---|---|
| low | 1 | 2 |
| medium | 5 | 11 |
| high | 20 | 41 |
| auto | 20 | 41 |

If cost matters, set both `quality` and resolution explicitly.

### Seedream 5 Pro and Lite

`seedream5_pro` costs 5 credits per 1K image or 9 per 2K image. `seedream5_lite` costs a flat 4 credits at any resolution.

### Gemini image models

`gemini_image3_pro` is the premium tier: 20 credits for a 1K or 2K image, 40 for 4K.

`gemini_2.5_flash` is a flat 5 credits at any resolution, and accepts reference images with aspect ratios between 0.25 and 4 — the widest tolerance of any model.

`gemini_image3.1_flash` is also available; see the [API reference](../endpoints/README.md) for its parameters.

<!-- /widget -->

## Upscaling images

`magnific_precision_upscaler_v2` costs **25 credits per image**, rising to **150 credits** when the output exceeds 4096px. Check your target dimensions before batching — the jump is sixfold.

## Reference image guidance

Reference images may be resized if they are too large or too small for the model you chose. Avoid references smaller than 640×640px or larger than 4K.

Each model enforces its own aspect ratio window on references — the full matrix is in [inputs](../assets/inputs.md#input-asset-aspect-ratio-requirements).

## Related

- [Image and audio pricing](../pricing/image-audio.md) — the full cost tables
- [Video models](./video.md) — if the output should move
- [Inputs](../assets/inputs.md) — codecs, size limits and reference rules

<!-- widget:cta -->

## Generate your first image

Two credits gets you a Gen-4 Image Turbo still — two cents.

[Create your account](https://dev.runwayml.com/) · [See the code](../get-started/using-the-api.md#generating-an-image)

<!-- /widget -->
