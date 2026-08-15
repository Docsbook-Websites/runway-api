---
title: Audio Models — Speech, Sound Effects, Dubbing and Isolation
description: Seven audio models for text-to-speech, sound design, dubbing and voice isolation, billed by character or by second from 0.25 credits.
---

# Audio models

Seven models cover generation and transformation of audio. Text-to-speech models bill per character; transformation models bill per second of input.

| Model | Input | Does | Cost |
|---|---|---|---|
| `seed_audio` | Text or Audio | Speech and sound effects | 0.25 credits/second (5 credit minimum) |
| `eleven_v3` | Text | Text to speech | 1 credit per 50 characters (1 credit minimum) |
| `eleven_multilingual_v2` | Text | Multilingual text to speech | 1 credit per 50 characters |
| `eleven_text_to_sound_v2` | Text | Sound effects | 1 credit/second, or 2 credits with no duration given |
| `eleven_voice_isolation` | Audio | Strips background from a voice track | 1 credit per 6s |
| `eleven_voice_dubbing` | Audio | Dubs into another language | 1 credit per 2s |
| `eleven_multilingual_sts_v2` | Audio | Speech-to-speech conversion | 1 credit per 3s |

## Picking one

**Narration and dialogue.** `eleven_v3` is the current-generation text-to-speech model; `eleven_multilingual_v2` when you need many languages from one voice. Both bill per 50 characters, so a 500-character paragraph is 10 credits — ten cents.

**Sound effects.** `eleven_text_to_sound_v2` generates a sound from a description. Supplying a duration bills 1 credit per second; omitting it bills a flat 2 credits, which is usually cheaper for short effects.

**Cleaning up recordings.** `eleven_voice_isolation` removes background noise from a voice track at 1 credit per 6 seconds — a ten-minute recording is 100 credits.

**Localising existing audio.** `eleven_voice_dubbing` is the most expensive transformation at 1 credit per 2 seconds, because it transcribes, translates and re-synthesises.

**Changing a voice without changing the words.** `eleven_multilingual_sts_v2` maps one performance onto another voice at 1 credit per 3 seconds.

**Runway's own model.** `seed_audio` handles both text-to-speech and sound effects at 0.25 credits per second — the cheapest per-second rate here — with a 5-credit minimum per generation.

## Supported audio formats

Models that accept audio take these containers and codecs:

| Container | Extension | Content type | Codecs |
|---|---|---|---|
| MP3 | `.mp3` | `audio/mpeg`, `audio/mp3` | MP3 (MPEG-1/2 Layer 3) |
| WAV | `.wav` | `audio/wav`, `audio/wave`, `audio/x-wav` | PCM (uncompressed) |
| FLAC | `.flac` | `audio/flac`, `audio/x-flac` | FLAC (lossless) |
| M4A | `.m4a` | `audio/mp4`, `audio/x-m4a` | AAC, ALAC |
| AAC | `.aac` | `audio/aac`, `audio/x-aac` | AAC (raw) |

Audio inputs are capped at 32MB by URL and 16MB as a data URI. Above that, use [ephemeral uploads](../assets/uploads.md), which take up to 200MB.

The `Content-Type` header must match the actual media type — file extensions in URLs are ignored, and `application/octet-stream` is explicitly rejected.

## Related

- [Image and audio pricing](../pricing/image-audio.md) — full cost tables
- [Inputs](../assets/inputs.md) — size limits and header requirements
- [Video models](./video.md) — `veo3.1` generates video with native audio

<!-- widget:cta -->

## Add a voice to your product

A 500-character narration costs 10 credits — ten cents.

[Create your account](https://dev.runwayml.com/) · [Browse all models](./README.md)

<!-- /widget -->
