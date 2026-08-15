---
title: HTTP Error Codes and Retry Behaviour
description: Every HTTP status the Runway API returns, what causes it, and whether retrying is safe — plus the backoff and jitter strategy to retry with.
---

# HTTP errors

The first question about any error is whether retrying helps. This table answers it.

| Status | Description | Retry? |
|---|---|---|
| `400` | A problem with one of the request's inputs. The JSON response carries an `error` member with a human-readable explanation. | **No** |
| `401` | The API key is not valid. | **No** |
| `404` | The referenced resource is not available. | **No** |
| `405` | The endpoint was called with an unsupported HTTP method. | **No** |
| `429` | Too many requests in a period, or an integration limit was reached. | **Yes** |
| `502` | Runway is shedding load. | **Yes** |
| `503` | Runway is shedding load. | **Yes** |
| `504` | Runway is overloaded and could not satisfy the request. | **Yes** |

## Retrying correctly

When a status is retryable, use **exponential backoff with jitter**. Add a random delay of up to **50%** to each retry interval — without it, every client that failed at the same moment retries at the same moment, and the recovering service is knocked over again.

```js
async function withRetry(fn, maxAttempts = 5) {
  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (error) {
      const retryable = [429, 502, 503, 504].includes(error.status);
      if (!retryable || attempt === maxAttempts - 1) throw error;

      const base = 1000 * 2 ** attempt;
      const jitter = Math.random() * base * 0.5;
      await new Promise((r) => setTimeout(r, base + jitter));
    }
  }
}
```

**Runway's SDKs handle retries automatically.** The code above is for direct REST integrations; if you use the Node.js or Python SDK, you already have this behaviour.

## Reading each code

**`400 Bad Request`** almost always means an input violated a documented constraint — an oversized asset, an unsupported codec, a `Content-Type` of `application/octet-stream`, a malformed data URI, or an aspect ratio outside the model's window. The `error` member names the problem. Fix the input; never retry.

**`401 Unauthorized`** means the key is invalid, disabled or absent. Check that `RUNWAYML_API_SECRET` is set in the environment the code actually runs in.

**`404 Not Found`** on task retrieval usually means a mistyped ID. Note that a `404` from an idempotent delete is expected and harmless — exclude it from your error-rate alerting.

**`429 Too Many Requests`** means you hit your [tier's daily generation cap](../usage/tiers.md) or another integration limit. There is **no requests-per-minute limit** as long as you stay within your daily generation allowance — excess concurrent work is queued as `THROTTLED` rather than rejected. A persistent `429` therefore points at the daily cap, and the fix is tiering up rather than backing off harder.

**`502`, `503`, `504`** are Runway-side. Retry with backoff; they resolve.

## Errors versus task failures

An HTTP error means the *request* failed. A task that is accepted (`200`) can still fail later during generation, reporting a `failureCode` instead. Those are documented separately in [task failures](./task-failures.md) — and the retry rules there are different, including one case where credits are not refunded.

## Related

- [Task failures](./task-failures.md) — failures after a task is accepted
- [Go-live checklist](../get-started/go-live.md) — testing your failure handling
- [Usage tiers](../usage/tiers.md) — the limits behind most `429`s

<!-- widget:cta -->

## Test failures before your users find them

The go-live checklist covers the failure modes that actually appear in production.

[Read the checklist](../get-started/go-live.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
