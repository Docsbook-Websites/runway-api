---
title: Assets — Inputs, Outputs and Uploads
description: What you can send to the Runway API, what comes back, and how to move files larger than the inline limits allow.
---

# Assets

Three pages cover everything that goes into a generation and everything that comes out.

<!-- widget:cards -->

- [Inputs](./inputs.md) — Size limits, codecs, header requirements and aspect ratio windows {file-input}
- [Outputs](./outputs.md) — Result URLs, task statuses, and why you must re-host {file-output}
- [Ephemeral uploads](./uploads.md) — Files up to 200MB with a 24-hour `runway://` URI {upload}

<!-- /widget -->

## The three rules that catch people out

**`Content-Type` must be correct.** File extensions in URLs are ignored entirely, and generic values such as `application/octet-stream` are rejected. Object storage that defaults to octet-stream is a common source of `400 Bad Request`.

**Base64 inflates by ~33%.** The data URI limit applies to the encoded string, so the 5MB image cap means a 3.3MB original file.

**Output URLs expire in 24–48 hours.** Download and re-host every result before showing it to users — never persist a Runway URL.

## Related

- [Models](../models/README.md) — per-model input capabilities
- [Task failures](../errors/task-failures.md) — what `ASSET.INVALID` means
- [Making API calls](../get-started/using-the-api.md) — passing assets in code

<!-- widget:cta -->

## Check your assets before you launch

Most launch-day `400`s are input validation misses the inputs reference already documents.

[Read the inputs reference](./inputs.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
