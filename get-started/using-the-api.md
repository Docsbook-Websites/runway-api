---
title: Making API Calls — Your First Video and Image
description: Generate video from text or an image and stills from a prompt, then learn how to wait for results correctly with polling, timeouts and abort signals.
---

# Making API calls

Every generation follows the same shape: create a task, wait for it, read the output URL. This page covers all three for video and images.

You need an API key first — see [account setup](./setup.md).

## Install an SDK

The SDKs give you type safety and correct polling behaviour for free. Use them unless you have a reason not to.

```bash
npm install --save @runwayml/sdk
```

```bash
pip install runwayml
```

The Node.js SDK ships TypeScript bindings and supports Node 18+. The Python SDK ships MyPy-compatible type annotations and supports Python 3.8+.

## Image-to-video

Give Gen-4.5 a still image and a prompt describing the motion you want:

```js
import RunwayML, { TaskFailedError } from '@runwayml/sdk';

const client = new RunwayML();

try {
  const task = await client.imageToVideo
    .create({
      model: 'gen4.5',
      promptImage: 'https://example.com/your-image.jpg',
      promptText: 'A timelapse on a sunny day with clouds flying by',
      ratio: '1280:720',
      duration: 5,
    })
    .waitForTaskOutput();

  console.log('Video URL:', task.output[0]);
} catch (error) {
  if (error instanceof TaskFailedError) {
    console.error('The video failed to generate.', error.taskDetails);
  } else {
    throw error;
  }
}
```

`promptImage` accepts an HTTPS URL, a base64 data URI, or a `runway://` URI from an [ephemeral upload](../assets/uploads.md). All three work anywhere a URL is accepted.

## Text-to-video

Gen-4.5 also generates video from text alone. Omit `promptImage`:

```js
const task = await client.imageToVideo
  .create({
    model: 'gen4.5',
    promptText: 'A serene mountain landscape at sunrise with mist rolling through the valleys',
    ratio: '1280:720',
    duration: 5,
  })
  .waitForTaskOutput();
```

Text-to-video on Gen-4.5 supports landscape `1280:720` and portrait `720:1280` only. Image-to-video supports a wider set — see [aspect ratios](../assets/inputs.md#aspect-ratios-and-auto-cropping).

## Passing a local file as a data URI

If the image lives on disk, base64-encode it instead of uploading it somewhere first:

```js
import fs from 'node:fs';
import RunwayML from '@runwayml/sdk';

const client = new RunwayML();

const imageBuffer = fs.readFileSync('example.png');
const dataUri = `data:image/png;base64,${imageBuffer.toString('base64')}`;

const task = await client.imageToVideo
  .create({
    model: 'gen4.5',
    promptImage: dataUri,
    promptText: 'A timelapse on a sunny day with clouds flying by',
    ratio: '1280:720',
    duration: 5,
  })
  .waitForTaskOutput();
```

Data URIs are capped at **5MB for images** and **16MB for video and audio** — and base64 inflates a file by about 33%, so a 5MB budget means a 3.3MB original. Above that, use [ephemeral uploads](../assets/uploads.md), which take files up to 200MB.

## Generating an image

Stills use `textToImage`:

```js
const imageTask = await client.textToImage
  .create({
    model: 'gen4_image',
    promptText: 'A beautiful sunset over a calm ocean',
    ratio: '1360:768',
  })
  .waitForTaskOutput();

console.log(imageTask.output[0]);
```

## Waiting for output

Generations are asynchronous. `create` returns a task ID straight away:

```json
{ "id": "17f20503-6c24-4c16-946b-35dbbce2af2f" }
```

The task then moves to `SUCCEEDED`, `FAILED` or `CANCELED`. You can poll `GET /v1/tasks/{id}` yourself:

```js
const task = await client.tasks.retrieve('17f20503-6c24-4c16-946b-35dbbce2af2f');
console.log(task.status); // "PENDING"
```

If you do poll manually, use an interval of **5 seconds or more**, add jitter, and back off exponentially on non-200 responses. Avoid fixed-interval polling with `setInterval` — API latency makes the calls stack up.

### Use `waitForTaskOutput` instead

Every `create` method returns a promise carrying a `waitForTaskOutput` helper that polls correctly for you.

The one rule: call it on the **unawaited** result of `create`.

```js
// ✅ Correct — chain onto create()
const imageTask = await client.textToImage
  .create({ model: 'gen4_image', promptText: 'A sunset', ratio: '1360:768' })
  .waitForTaskOutput();
```

```js
// ❌ Wrong — awaiting create() first loses the helper
const awaited = await client.textToImage.create({ /* ... */ });
await awaited.waitForTaskOutput(); // not available
```

To keep the task ID for your own records and still wait for the result, await the create promise separately:

```js
// Note: no await here
const imageTask = client.textToImage.create({
  model: 'gen4_image',
  promptText: 'A beautiful sunset over a calm ocean',
  ratio: '1360:768',
});

const taskId = (await imageTask).id; // store this in your database
const completed = await imageTask.waitForTaskOutput();

console.log(completed.output[0]);
```

`tasks.retrieve` carries the same helper, so you can create a task in one request and wait for it in another:

```js
const task = await client.tasks
  .retrieve('17f20503-6c24-4c16-946b-35dbbce2af2f')
  .waitForTaskOutput();
```

### Timeouts and cancellation

`waitForTaskOutput` times out after ten minutes by default and throws `TaskTimedOutError`. Pass your own timeout and an `AbortSignal`:

```js
const task = await client.textToImage
  .create({ model: 'gen4_image', promptText: 'A sunset', ratio: '1360:768' })
  .waitForTaskOutput({
    timeout: 5 * 60 * 1000,
    abortSignal: myAbortSignal,
  });
```

Passing `null` waits indefinitely. Don't — if you hit your concurrency limit or Runway has an outage, indefinite waits pile up on your server.

When you generate inside a request handler, abort polling if the client disconnects:

```js
app.post('/generate-image', async (req, res) => {
  const abortController = new AbortController();
  req.on('close', () => abortController.abort());

  try {
    const imageTask = await runway.textToImage
      .create({ model: 'gen4_image', promptText: req.body.prompt, ratio: '1360:768' })
      .waitForTaskOutput({ abortSignal: abortController.signal });

    res.send(imageTask.output[0]);
  } catch (error) {
    if (error instanceof TaskFailedError) {
      res.status(500).send('Task failed');
    } else {
      throw error;
    }
  }
});
```

Add rate limiting to any endpoint that triggers a generation on user input — it spends your credits.

**Aborting or timing out does not cancel the task.** Polling stops; the generation keeps running and still costs credits. To actually stop it, call the cancellation endpoint.

### Handling failure

A task that reaches `FAILED` throws `TaskFailedError`, with the full `tasks.retrieve` payload on `error.taskDetails`:

```js
import { TaskFailedError } from '@runwayml/sdk';

try {
  const imageTask = await client.textToImage
    .create({ model: 'gen4_image', promptText: 'A sunset', ratio: '1360:768' })
    .waitForTaskOutput();
} catch (error) {
  if (error instanceof TaskFailedError) {
    console.error('Task failed:', error.taskDetails);
  } else {
    throw error;
  }
}
```

The `failureCode` inside `taskDetails` tells you whether a retry is worth attempting — see [task failures](../errors/task-failures.md).

## Endpoint-to-method map

| Operation | Endpoint | Node.js method |
|---|---|---|
| Generate an image | `POST /v1/text_to_image` | `client.textToImage.create` |
| Generate a video | `POST /v1/image_to_video` | `client.imageToVideo.create` |
| Character performance | `POST /v1/character_performance` | `client.characterPerformance.create` |
| Retrieve a task | `GET /v1/tasks/{id}` | `client.tasks.retrieve` |
| Upload a file | `POST /v1/uploads` | `client.uploads.createEphemeral` |

The full list lives in the [API reference](../endpoints/README.md).

## Related

- [API reference](../endpoints/README.md) — every endpoint, with a live playground
- [Models](../models/README.md) — every model ID and what it accepts
- [Inputs](../assets/inputs.md) — codecs, size limits and aspect ratio rules
- [Outputs](../assets/outputs.md) — why you must re-host result URLs
- [Go-live checklist](./go-live.md) — before real users hit this code

<!-- widget:cta -->

## Run it against your own key

The code on this page works as-is once `RUNWAYML_API_SECRET` is set.

[Get an API key](https://dev.runwayml.com/) · [See what it costs](../pricing/README.md)

<!-- /widget -->
