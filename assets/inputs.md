---
title: API Inputs — Size Limits, Codecs and Aspect Ratios
description: Every constraint on assets you send to the Runway API — URL and data URI size limits, supported codecs, header requirements and per-model aspect ratio windows.
---

# Inputs

Assets reach the API three ways: an HTTPS URL, a base64 data URI, or a `runway://` URI from an [ephemeral upload](./uploads.md). Each has different limits.

## Size limits

| Input type | URL | Data URI | Ephemeral upload |
|---|---|---|---|
| Image | 16MB | 5MB | 200MB |
| Video | 32MB | 16MB | 200MB |
| Audio | 32MB | 16MB | 200MB |

Base64 encoding inflates a file by about **33%**, and the data URI limit applies to the *encoded* size. To fit inside the 5MB image limit, the original file must be 3.3MB or smaller.

## Requirements for URLs

Every URL you pass must satisfy all of these:

- **HTTPS only.**
- The hostname must be a **domain name**, not an IP address.
- The server must return valid `Content-Type` and `Content-Length` headers.
- **Redirects are not followed.** A 3XX response fails the request.
- The URL must be at most **2048 characters**.
- The server must support **HTTP HEAD** requests.

### Content-Type must be correct

The `Content-Type` response header must match the actual media type. **File extensions in URLs are ignored.** Generic values including `application/octet-stream` are explicitly not supported — a common cause of `400 Bad Request` when serving from object storage that defaults to octet-stream.

### Allowlist Runway's user agent

Runway sends a `User-Agent` beginning with `RunwayML API/` when fetching your assets. If a WAF or scraping-prevention tool sits in front of your storage, allowlist that prefix or fetches will fail.

## Data URIs

Data URIs are accepted anywhere a URL is, unless a field says otherwise.

- The content type must be declared: `data:image/jpg;base64,...`
- The `base64` extension is required. The format is `data:content/type;base64,`
- The size limit applies to the encoded string.

Data URIs that break these rules are rejected with `400 Bad Request`.

## Choosing between URL, data URI and upload

**Data URIs** remove an upload step, which is convenient if the asset is not already in object storage. But they travel inside the request body: the whole payload is sent serially, and the JSON must be parsed before assets can be extracted, so both request time and server latency rise.

**URLs** are processed in parallel and immediately, so the gap between your request and the start of processing is much smaller. The cost moves to the latency between Runway's servers and yours — a CDN or object storage service minimises it.

**[Ephemeral uploads](./uploads.md)** give you object-storage behaviour without running object storage. Files go to Runway, and you get a `runway://` URI valid for 24 hours, usable anywhere an HTTPS URL is. They are rate limited and require a minimum file size of 512 bytes.

## Supported formats

<!-- widget:accordion -->

### Images

| Codec | Content-Type header |
|---|---|
| JPEG | `image/jpg` or `image/jpeg` |
| PNG | `image/png` |
| WebP | `image/webp` |

**GIF is not supported.**

### Video

| Container | Extension | Content type | Codecs |
|---|---|---|---|
| MP4 | `.mp4` | `video/mp4` | H.264, H.265/HEVC, AV1 |
| QuickTime | `.mov` | `video/quicktime` | H.264, H.265/HEVC, Apple ProRes (422 Proxy, 422 LT, 422, 422 HQ, 4444, 4444 XQ) |
| Matroska | `.mkv` | `video/x-matroska` | H.264, H.265/HEVC, VP8, VP9, AV1 |
| WebM | `.webm` | `video/webm` | VP8, VP9, AV1 |
| 3GPP | `.3gp` | `video/3gpp` | H.264 |
| Ogg | `.ogv` | `video/ogg` | Theora |

Supported but discouraged, for quality, performance, industry support or file size reasons:

| Container | Extension | Content type | Codecs |
|---|---|---|---|
| QuickTime | `.mov` | `video/quicktime` | MJPEG |
| Matroska | `.mkv` | `video/x-matroska` | MPEG2 (H.262) |
| AVI | `.avi` | `video/x-msvideo` | H.264, MJPEG, MSMPEG4v3 |
| Flash Video | `.flv` | `video/x-flv` | FLV1, H.264 |
| MPEG | `.mpg`, `.mpeg` | `video/mpeg` | MPEG2 (H.262) |

H.264/AVC is the most widely supported modern codec and the safest default. Extensions above are for reference only — the API does not consider them.

### Audio

| Container | Extension | Content type | Codecs |
|---|---|---|---|
| MP3 | `.mp3` | `audio/mpeg`, `audio/mp3` | MP3 (MPEG-1/2 Layer 3) |
| WAV | `.wav` | `audio/wav`, `audio/wave`, `audio/x-wav` | PCM (uncompressed) |
| FLAC | `.flac` | `audio/flac`, `audio/x-flac` | FLAC (lossless) |
| M4A | `.m4a` | `audio/mp4`, `audio/x-m4a` | AAC, ALAC |
| AAC | `.aac` | `audio/aac`, `audio/x-aac` | AAC (raw) |

<!-- /widget -->

## Aspect ratios and auto-cropping

The `ratio` parameter sets the **output** dimensions. If your input asset does not match, the model **auto-crops from the centre** to the ratio you gave.

Supported ratios differ per model:

- **Gen-4.5 text-to-video:** landscape `1280:720` and portrait `720:1280` only.
- **Gen-4.5 image-to-video:** landscape `1280:720`, `1584:672`, `1104:832`; portrait `720:1280`, `832:1104`, `672:1584`; square `960:960`.
- **Gen-4 Turbo and Act-Two:** landscape `1280:720`, `1584:672`, `1104:832`; portrait `720:1280`, `832:1104`; square `960:960`.
- **Aleph 2.0:** preserves the input video's resolution, up to 1080p. Inputs must be 2–30 seconds at 30 FPS or lower.
- **Seedance 2.5:** 12 ratios across 480p and 720p only, default `1280:720`, durations 4–30 seconds. Input and reference videos must be at least 480p. At 480p, `864:496` and `496:864` deliver standard 854×480 / 480×854. Video-to-video takes `mode: "reference"` (default) or `mode: "extend"`.
- **Seedance 2.0:** 24 ratios across 480p, 720p, 1080p and 4K. **Fast** and **Mini** support 12 ratios across 480p and 720p only.
- **Hailuo 3.0:** `adaptive`, `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, at `768P` or `2K` (default `2K`), 5–15 seconds. `adaptive` requires at least one reference image or video. Keyframe and unpositioned reference-image modes cannot be mixed; reference audio is valid only with unpositioned reference images.
- **Grok Imagine Video 1.5 text-to-video:** `1:1`, `16:9`, `9:16`, `4:3`, `3:4`, `3:2`, `2:3` at 480p/720p/1080p (default 480p), 1–15 seconds. Up to 7 image references (addressable as `[Image 1]`, `[Image 2]`…) and 3 audio references of 3–15 seconds. Audio references require an image reference, and any request with image references caps at 720p.
- **Grok Imagine Video 1.5 image-to-video:** uses `resolution`; output ratio follows the input image; single first frame only.
- **HappyHorse 1.0 text-to-video:** `1280:720`, `1920:1080`, `720:1280`, `1080:1920`, `960:960`, `1440:1440`, `1108:832`, `1662:1248`, `832:1108`, `1248:1662`. Image-to-video uses `resolution` (720P/1080P) and follows the input ratio; prompt images must be at least 300px per side.
- **Gemini Omni Flash:** text- and image-to-video output `1280:720` or `720:1280`, always 720p. Video-to-video has **no `ratio` parameter** — 720p, orientation matched to input, inputs at most 10 seconds.
- **Magnific Video Upscaler:** inputs up to 30 seconds; `resolution` of `720p`, `1k`, `2k` (default) or `4k`.

Third-party models — `veo3.1`, `veo3.1_fast`, `grok_imagine_1_5`, `grok_imagine_image_2`, `happyhorse_1_0`, `gemini_omni_flash` and others — may crop or resize inputs in ways that differ from the above.

## Reference images

References may be resized if they are too large or too small for the model. Avoid references below 640×640px or above 4K.

### Input asset aspect ratio requirements

For models accepting image or video references, `width ÷ height` must fall inside these windows:

| Model | Input type | Min | Max |
|---|---|---|---|
| Gen-4 Turbo image-to-video | Prompt image | 0.5 | 2.358 |
| Gen-4 image-to-video | Prompt image | 0.5 | 2.358 |
| Gen-4.5 image-to-video | Prompt image | 0.5 | 2 |
| Act-Two | Character image | 0.5 | 2.358 |
| Act-Two | Character video | 0.5 | 2.358 |
| Act-Two | Reference video | 0.5 | 2.358 |
| Seedance 2.5 image-to-video | Prompt image (first/last/reference) | 0.4 | 4 |
| Seedance 2.5 text-to-video | Reference image | 0.4 | 4 |
| Seedance 2.5 video-to-video | Reference image | 0.4 | 4 |
| Seedance 2 image-to-video | Prompt image (first/last/reference) | 0.4 | 4 |
| Seedance 2 text-to-video | Reference image | 0.4 | 4 |
| Seedance 2 video-to-video | Reference image | 0.4 | 4 |
| Seedance 2.0 Fast image-to-video | Prompt image (first/last/reference) | 0.4 | 4 |
| Seedance 2.0 Fast text-to-video | Reference image | 0.4 | 4 |
| Seedance 2.0 Fast video-to-video | Reference image | 0.4 | 4 |
| Seedance 2.0 Mini image-to-video | Prompt image (first/last/reference) | 0.4 | 4 |
| Seedance 2.0 Mini text-to-video | Reference image | 0.4 | 4 |
| Seedance 2.0 Mini video-to-video | Reference image | 0.4 | 4 |
| Hailuo 3.0 image-to-video | Prompt image (first/last/reference) | 0.2 | 4 |
| Hailuo 3.0 text-to-video | Reference image | 0.2 | 4 |
| Hailuo 3.0 video-to-video | Reference image | 0.2 | 4 |
| Veo 3.1 / 3.1 Fast image-to-video | First/last keyframes | 0.5 | 2 |
| HappyHorse 1.0 image-to-video | Prompt image | 0.55 | 1.8 |
| Gemini Omni Flash image-to-video | Prompt image | 0.5 | 2 |
| Gemini 2.5 Flash text-to-image | Reference image | 0.25 | 4 |

## Related

- [Ephemeral uploads](./uploads.md) — for files above the data URI limits
- [Outputs](./outputs.md) — what comes back and how long it lasts
- [HTTP errors](../errors/http-errors.md) — what a rejected input returns

<!-- widget:cta -->

## Validate before you launch

Most `400` errors at launch are input-validation misses. The go-live checklist catches them.

[Read the checklist](../get-started/go-live.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
