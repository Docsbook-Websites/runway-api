---
title: Task Failure Codes — Causes, Retries and Refunds
description: Decode every failureCode the Get Task endpoint returns, learn which failures are worth retrying, and find the one case where credits are not refunded.
---

# Task failures

A task that was accepted can still fail during processing. `GET /v1/tasks/{id}` then returns a `failureCode` naming what went wrong.

Branch on it. The right response ranges from "retry immediately" to "never retry, and you have been charged".

## Quick reference

| Failure code | Retry? | Credits refunded? |
|---|---|---|
| `SAFETY.INPUT.*` | **Never** | **No** |
| `SAFETY.OUTPUT.*` | No | Yes |
| `INPUT_PREPROCESSING.SAFETY.TEXT` | **Never** | Yes |
| `INPUT_PREPROCESSING.INTERNAL` | Yes, after a delay | Yes |
| `INTERNAL.BAD_OUTPUT.*` | Yes, after fixing the prompt | Yes |
| `ASSET.INVALID` | No | Yes |
| `THIRD_PARTY.UNAVAILABLE` | Yes, after a wait | Yes |
| `INTERNAL` or null | Yes, after a delay | Yes |

<!-- widget:accordion -->

### SAFETY.* — content moderation

Codes beginning `SAFETY.` mean the task was rejected by content moderation, which runs on both inputs and outputs.

`SAFETY.INPUT.*` means an input was rejected; `SAFETY.OUTPUT.*` means the generated result was. The final component indicates the likely source — `SAFETY.INPUT.TEXT` points at prompt text. Where several inputs would be rejected, you get the first one tested.

**Treat the third component as diagnostic only.** Runway makes a best-effort attempt at accuracy, but it may not correspond exactly to your inputs — you can receive `SAFETY.INPUT.TEXT` on a task that passed no `promptText` at all. **Do not surface these codes to end users.**

**Credits are not refunded for `SAFETY.INPUT.*` failures**, unlike every other failure type. Do not retry them: you will be charged again for the same rejection.

Repeated moderated requests can lead to account suspension. If user-supplied prompts reach the API, moderate them upstream.

### INTERNAL.BAD_OUTPUT.* — rejected for quality

These generations were rejected by Runway's internal systems for quality or system-error reasons. `INTERNAL.BAD_OUTPUT.01` is by far the most common.

Usual causes:

- Logos, watermarks or overlaid text in the input media
- A prompt that explicitly asks for text to be generated
- A prompt asking for *a prompt to be written* rather than giving one directly — for example "write a prompt for a sunset" instead of "a sunset"

Retrying is worthwhile **if you correct the prompt or input first**. An unchanged retry will usually fail the same way.

### INPUT_PREPROCESSING.SAFETY.TEXT

Input prompt text was rejected for content moderation reasons. Do not retry.

### INPUT_PREPROCESSING.INTERNAL

Something went wrong performing content moderation itself, rather than the content failing it. Retry, but add a delay first.

### ASSET.INVALID

One of your inputs is unacceptable for this task type — typically wrong dimensions, wrong duration, or another media property outside the model's limits.

Do not retry: the same asset will fail again. Check the asset against the [inputs reference](../assets/inputs.md), particularly the per-model aspect ratio windows and duration ceilings.

### THIRD_PARTY.UNAVAILABLE

A third-party-provided model failed to return output, usually because of an upstream outage or load shedding. Affects models such as `veo3.1`, `grok_imagine_1_5`, `happyhorse_1_0` and `gemini_omni_flash`.

Do not retry immediately — an immediate retry is unlikely to succeed. Wait, then retry. If your product can tolerate it, falling back to a Runway-native model such as `gen4.5` is a good degradation path.

### INTERNAL, or a null value

An internal problem processing the task. Retry with a delay.

<!-- /widget -->

## Handling failures in code

`waitForTaskOutput` throws `TaskFailedError` when a task fails, with the full retrieve payload on `error.taskDetails`:

```js
import { TaskFailedError } from '@runwayml/sdk';

const NEVER_RETRY = ['ASSET.INVALID', 'INPUT_PREPROCESSING.SAFETY.TEXT'];

try {
  const task = await client.imageToVideo
    .create({ model: 'gen4.5', promptText: prompt, ratio: '1280:720', duration: 5 })
    .waitForTaskOutput();

  return task.output[0];
} catch (error) {
  if (!(error instanceof TaskFailedError)) throw error;

  const code = error.taskDetails?.failureCode ?? 'INTERNAL';

  if (code.startsWith('SAFETY.INPUT')) {
    // Not refunded, never retry. Show the user a generic message —
    // the code itself is diagnostic only and may not be accurate.
    return rejectWithGenericMessage();
  }

  if (NEVER_RETRY.includes(code)) return reportInvalidInput(code);

  return scheduleRetryWithDelay(code);
}
```

## Related

- [HTTP errors](./http-errors.md) — failures at the request level
- [Inputs](../assets/inputs.md) — the constraints behind `ASSET.INVALID`
- [Go-live checklist](../get-started/go-live.md) — testing failure paths before launch

<!-- widget:cta -->

## Handle failures before they reach users

The go-live checklist walks through every failure path this page describes.

[Read the checklist](../get-started/go-live.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
