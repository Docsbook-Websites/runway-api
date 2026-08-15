---
title: Runway API Models — Video, Image, Audio and Real-Time
description: Every model the Runway API exposes, what it takes as input, and which one to reach for. Compare Gen-4.5, Seedance 2.5, Aleph 2.0 and 30 more.
---

# Models

The API exposes more than thirty models across video, image, audio, upscaling and real-time avatars. This page is the index; each category page below explains what to pick and why.

Don't want to choose? A [model router](./routers.md) picks per request based on your cost, latency or quality preference, at no extra charge.

<!-- widget:cards -->

- [Video models](./video.md) — 14 models from 5 credits/second to cinematic 30-second output {clapperboard}
- [Image models](./image.md) — 9 models for stills, references and campaign assets {image}
- [Audio models](./audio.md) — Speech, sound effects, dubbing and voice isolation {audio-waveform}
- [Model routers](./routers.md) — Automatic per-request model selection, no extra cost {route}

<!-- /widget -->

## Choosing a video model

| If you need | Use | Cost |
|---|---|---|
| Up to 30 seconds, cinematic, large reference budget | `seedance2_5` | 20–30 credits/second |
| Best motion quality and prompt adherence | `gen4.5` | 12 credits/second |
| Lowest cost per second | `gen4_turbo` | 5 credits/second |
| Editing an existing video with a prompt | `aleph2` | 28 credits/second |
| Native audio with the video | `veo3.1` | 40 credits/second with audio |
| Driving a character's performance | `act_two` | 5 credits/second |
| 4K output | `seedance2` | 150 credits/second |

See [video models](./video.md) for resolutions, durations and reference limits.

## Choosing an image model

| If you need | Use | Cost |
|---|---|---|
| The cheapest usable image | `gen4_image_turbo` | 2 credits, any resolution |
| A production still | `gen4_image` | 5 credits (720p) / 8 (1080p) |
| Highest fidelity at 4K | `gemini_image3_pro` | 40 credits per 4K image |
| Precise text rendering | `gpt_image_2` | 1–41 credits by quality |

See [image models](./image.md) for reference-image behaviour and quality tiers.

## Full model list

### Video generation

| Model | Input | Output |
|---|---|---|
| `seedance2_5` | Text, Image, or Video | Video |
| `grok_imagine_1_5` | Text or Image | Video |
| `seedance2` | Text, Image, or Video | Video |
| `seedance2_fast` | Text, Image, or Video | Video |
| `seedance2_mini` | Text, Image, or Video | Video |
| `hailuo3` | Text, Image, or Video | Video |
| `aleph2` | Video + Text/Image | Video |
| `gen4.5` | Text or Image | Video |
| `gen4_turbo` | Image | Video |
| `act_two` | Image or Video | Video |
| `veo3.1` | Text or Image | Video |
| `veo3.1_fast` | Text or Image | Video |
| `happyhorse_1_0` | Text or Image | Video |
| `gemini_omni_flash` | Text, Image, or Video | Video |

### Image generation

| Model | Input | Output |
|---|---|---|
| `grok_imagine_image_2` | Text/Image (references) | Image |
| `seedream5_pro` | Text/Image (references) | Image |
| `seedream5_lite` | Text/Image (references) | Image |
| `gen4_image` | Text/Image (references) | Image |
| `gen4_image_turbo` | Text + Image (references) | Image |
| `gemini_image3_pro` | Text/Image (references) | Image |
| `gemini_image3.1_flash` | Text/Image (references) | Image |
| `gpt_image_2` | Text/Image (references) | Image |
| `gemini_2.5_flash` | Text/Image (references) | Image |

### Audio generation

| Model | Input | Output |
|---|---|---|
| `seed_audio` | Text or Audio | Audio |
| `eleven_v3` | Text | Audio |
| `eleven_multilingual_v2` | Text | Audio |
| `eleven_text_to_sound_v2` | Text | Audio |
| `eleven_voice_isolation` | Audio | Audio |
| `eleven_voice_dubbing` | Audio | Audio |
| `eleven_multilingual_sts_v2` | Audio | Audio |

### Real-time

| Model | Input | Output |
|---|---|---|
| `gwm1_avatars` | Text (conversation) | Video + Audio |

### Upscaling

| Model | Input | Output |
|---|---|---|
| `magnific_precision_upscaler_v2` | Image | Image |
| `magnific_video_upscaler_creative` | Video | Video |

Video upscaling always uses `model: magnific_video_upscaler_creative` on `POST /v1/video_upscale`, and input videos can be at most 30 seconds.

## Related

- [Pricing](../pricing/README.md) — exact credit cost of every model above
- [Inputs](../assets/inputs.md) — per-model aspect ratio and reference limits
- [Making API calls](../get-started/using-the-api.md) — how to invoke any of them

<!-- widget:cta -->

## Try a model against your own prompt

The playground runs every model on this page without writing code.

[Open the playground](https://dev.runwayml.com/models) · [Compare pricing](../pricing/README.md)

<!-- /widget -->
