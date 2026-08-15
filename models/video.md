---
title: Video Generation Models — Gen-4.5, Seedance, Aleph and Veo
description: Compare all 14 Runway video models by resolution, duration, reference budget and cost per second, so you pick the right one before you write the call.
---

# Video models

Fourteen models generate video, spanning 5 to 150 credits per second. The differences that matter in practice are duration ceiling, resolution, and how many references each accepts.

## At a glance

| Model | Max duration | Resolutions | Cost/second |
|---|---|---|---|
| `seedance2_5` | 30s | 480p, 720p | 20–30 credits |
| `seedance2` | — | 480p–4K | 36–150 credits |
| `seedance2_fast` | — | 480p, 720p | 29 credits |
| `seedance2_mini` | — | 480p, 720p | 16 credits |
| `gen4.5` | — | 720p | 12 credits |
| `gen4_turbo` | — | 720p | 5 credits |
| `aleph2` | 30s input | Preserves input, up to 1080p | 28 credits |
| `act_two` | — | 720p | 5 credits |
| `veo3.1` | — | — | 20–40 credits |
| `veo3.1_fast` | — | — | 10–15 credits |
| `hailuo3` | 15s | 768P, 2K | 10–15 credits |
| `grok_imagine_1_5` | 15s | 480p, 720p, 1080p | 10–29 credits |
| `happyhorse_1_0` | — | 720p, 1080p | 15–30 credits |
| `gemini_omni_flash` | 10s input (v2v) | 720p | 10–11 credits |

Full per-resolution rates are on the [video pricing](../pricing/video.md) page.

<!-- widget:accordion -->

### Seedance 2.5 — long-form cinematic video

`seedance2_5` generates up to **30 seconds** with a large reference budget for images, video and audio. It is the model to use when a single clip needs to carry a narrative.

It supports 12 width:height ratios across 480p and 720p only, defaulting to `1280:720`. Durations run 4–30 seconds. Input and reference videos must be at least 480p, and combined input plus reference video is capped at 30 seconds.

Video-to-video accepts `mode: "reference"` (the default) or `mode: "extend"`.

At 480p, the ratios `864:496` and `496:864` deliver standard 854×480 and 480×854 output rather than the literal pixel counts.

**Cost:** 30 credits/second at 720p, 20 at 480p, plus half that per second of input and reference video. Reference images and audio are free. Minimum 80 credits per generation.

### Gen-4.5 — best motion quality and prompt adherence

`gen4.5` is Runway's flagship video model, generating from text alone or from a starting image.

Text-to-video supports landscape `1280:720` and portrait `720:1280` only. Image-to-video adds `1584:672`, `1104:832`, `832:1104`, `672:1584` and square `960:960`.

Prompt images must have an aspect ratio between 0.5 and 2.

**Cost:** 12 credits/second, so a five-second clip is 60 credits ($0.60).

### Gen-4 Turbo — the cheapest usable video

`gen4_turbo` takes an image and animates it at 5 credits/second, the joint-lowest rate in the catalogue. Use it for volume workloads where per-clip fidelity matters less than throughput.

It supports landscape `1280:720`, `1584:672`, `1104:832`, portrait `720:1280`, `832:1104` and square `960:960`. Prompt images must fall between aspect ratios 0.5 and 2.358.

### Aleph 2.0 — editing existing video

`aleph2` takes a video plus a text or image prompt and returns an edited video. It is the video-to-video workhorse rather than a generator.

It **preserves the input video's resolution**, up to 1080p. Input videos must be 2–30 seconds at 30 FPS or lower.

**Cost:** 28 credits/second, minimum 56 credits per generation.

### Act-Two — character performance

`act_two` drives a character in an image or video using a reference performance. Character images, character videos and reference videos must all fall between aspect ratios 0.5 and 2.358.

At 5 credits/second it is as cheap as Gen-4 Turbo.

### Veo 3.1 and Veo 3.1 Fast — video with native audio

`veo3.1` and `veo3.1_fast` generate from text or an image, with or without audio. Audio roughly doubles the cost: 40 credits/second with, 20 without, on `veo3.1`; 15 and 10 respectively on `veo3.1_fast`.

First and last keyframes must fall between aspect ratios 0.5 and 2.

### Hailuo 3.0 — flexible ratios including adaptive

`hailuo3` accepts `ratio` values `adaptive`, `21:9`, `16:9`, `4:3`, `1:1`, `3:4` and `9:16`, at `768P` or `2K` (default `2K`). Durations run 5–15 seconds.

`adaptive` requires at least one reference image or video. Keyframe mode (`position: first/last`) and unpositioned reference-image mode **cannot be mixed**, and reference audio is only valid alongside unpositioned reference images, never keyframes.

Reference video is billed at the same per-second rate as output, capped at 15 seconds. Reference images cost 2 credits each.

### Grok Imagine Video 1.5 — many references, addressable in the prompt

`grok_imagine_1_5` text-to-video supports `1:1`, `16:9`, `9:16`, `4:3`, `3:4`, `3:2` and `2:3` at 480p, 720p or 1080p (default 480p), for 1–15 seconds.

It accepts up to **7 image references**, addressable in the prompt as `[Image 1]`, `[Image 2]` and so on, plus 3 audio references of 3–15 seconds each. Audio references require at least one image reference, and any request carrying image references is capped at 720p.

Image-to-video instead uses a `resolution` parameter and follows the input image's aspect ratio, supporting only a single first frame via `promptImage`.

### HappyHorse 1.0

`happyhorse_1_0` text-to-video supports ten ratios across 720p and 1080p: `1280:720`, `1920:1080`, `720:1280`, `1080:1920`, `960:960`, `1440:1440`, `1108:832`, `1662:1248`, `832:1108`, `1662:1248`.

Image-to-video uses `resolution` (720P or 1080P, default 720P) and follows the input image's ratio. Prompt images must be at least 300px on each side and fall between aspect ratios 0.55 and 1.8.

### Gemini Omni Flash

`gemini_omni_flash` text-to-video and image-to-video output landscape `1280:720` or portrait `720:1280`, always at 720p.

Video-to-video has **no `ratio` parameter** — output is 720p with orientation matched to the input, and input videos can be at most 10 seconds.

<!-- /widget -->

## Upscaling video

`magnific_video_upscaler_creative` takes videos up to 30 seconds. Set `resolution` to `720p`, `1k`, `2k` (default) or `4k`, and always include the model on `POST /v1/video_upscale`.

It bills per output **frame**, not per second — see [video pricing](../pricing/video.md#video-upscaling) for the formula, including how `fpsBoost` changes the count.

## A note on third-party models

Models such as `veo3.1`, `grok_imagine_1_5`, `happyhorse_1_0` and `gemini_omni_flash` are provided by third parties. They may crop or resize inputs in ways that differ from Runway's own models, and they can return a `THIRD_PARTY.UNAVAILABLE` [task failure](../errors/task-failures.md) during an upstream outage.

## Related

- [Video pricing](../pricing/video.md) — exact per-second rates and minimums
- [Inputs](../assets/inputs.md) — the full aspect ratio matrix for every model
- [Image models](./image.md) — stills and references

<!-- widget:cta -->

## Compare them on your own prompt

Run the same prompt through several models in the playground before committing one to code.

[Open the playground](https://dev.runwayml.com/models) · [See video pricing](../pricing/video.md)

<!-- /widget -->
