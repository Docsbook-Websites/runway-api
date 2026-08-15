---
title: Ephemeral Uploads — Files up to 200MB
description: Upload assets straight to Runway storage and get a runway:// URI valid for 24 hours — object-storage behaviour without running object storage.
---

# Ephemeral uploads

URL and data URI inputs are capped well below what large source media needs — 5MB for an image data URI, 32MB for a video URL. Ephemeral uploads raise that to **200MB** and remove the need to host assets yourself.

You upload a file, get a `runway://` URI back, and use it anywhere a URL or data URI is accepted.

## Uploading with an SDK

Pass a Node `fs` stream, or a `File`-like object, to `createEphemeral`:

```js
import * as fs from 'node:fs';
import RunwayML from '@runwayml/sdk';

const client = new RunwayML();

const myFile = fs.createReadStream('./path/to/file.mp4');
const { uri } = await client.uploads.createEphemeral(myFile);

console.log(uri); // runway://...
```

Other types go through the `toFile` helper. A filename is required:

```js
import * as fs from 'node:fs/promises';
import RunwayML, { toFile } from '@runwayml/sdk';

const client = new RunwayML();

const { uri } = await client.uploads.createEphemeral(
  toFile(
    await fs.readFile('./path/to/file.mp4'),
    'file.mp4',
  ),
);
```

`toFile` accepts Blobs and Blob-like objects, `Buffer`, `ArrayBuffer`, typed arrays such as `Uint8Array`, `DataView`, `Response` and Response-like objects, and any async iterator yielding those types.

The resulting URI drops straight into a generation:

```js
const task = await client.imageToVideo
  .create({
    model: 'gen4.5',
    promptImage: uri, // runway://...
    promptText: 'A timelapse on a sunny day',
    ratio: '1280:720',
    duration: 5,
  })
  .waitForTaskOutput();
```

## Constraints

- URIs are valid for **24 hours** from creation. After that, re-upload.
- Maximum file size **200MB**; minimum **512 bytes**.
- You must have purchased credits to use the feature.
- Uploads are **rate limited**.

A URI can be reused across multiple generations within its 24-hour window, which saves bandwidth when the same asset feeds several requests. If you know a file will be used repeatedly, upload it once rather than passing it inline each time.

## Uploading without an SDK

Start the upload with `POST /v1/uploads`:

```json
{
  "filename": "filename.mp4",
  "type": "ephemeral"
}
```

`filename` must carry an extension representative of the file's contents. `type` must be `"ephemeral"`.

The response:

```json
{
  "uploadUrl": "https://...",
  "fields": { },
  "runwayUri": "runway://..."
}
```

POST to `uploadUrl` as multipart form data: send every key in `fields` as a form field, and the file contents as the `file` field. Once the upload succeeds, `runwayUri` is ready to use in a generation.

**If the upload fails, do not retry it.** Make a fresh `POST /v1/uploads` request and start over. See [HTTP errors](../errors/http-errors.md) for handling the failure modes robustly.

## Related

- [Inputs](./inputs.md) — size limits, codecs and the URL/data URI comparison
- [Outputs](./outputs.md) — result URLs expire too
- [API reference](../api/README.md) — full `/v1/uploads` details

<!-- widget:cta -->

## Skip the object storage setup

Ephemeral uploads handle 200MB files without infrastructure of your own.

[Create your account](https://dev.runwayml.com/) · [Read the inputs reference](./inputs.md)

<!-- /widget -->
