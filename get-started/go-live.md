---
title: Production Launch Checklist for the Runway API
description: Raise your limits, handle the failures that actually happen, lock down your API key and monitor the right metrics before real users reach your integration.
---

# Go-live checklist

The failures that hurt in production are rarely the ones you tested for. Work through these four areas before launch.

<!-- widget:accordion -->

### 1. Manage your usage

**Tier up before launch, not during it.** Your organization's concurrency and daily generation limits are set by its [usage tier](../usage/tiers.md). If you have only been testing, you are probably on Tier 1 — one concurrent generation, 50 per day. That will not survive a launch.

Tiers rise automatically with credits purchased, with no waiting period: $100 puts you on Tier 3 (5 concurrent, 1,000/day), $1,000 on Tier 4 (10 concurrent, 5,000/day). Estimate your peak daily generations and buy ahead of it.

**Set up autobilling.** Running out of credits mid-launch takes your feature offline. [Autobilling](../usage/autobilling.md) recharges automatically when your balance falls below a threshold you choose. Note that if your remaining monthly spend is below $10, autobilling fails — so watch your tier's monthly spend cap too.

### 2. Test your integration

**Test the failures, not just the happy path.** At minimum, your integration should survive:

- `429 Too Many Requests` — you hit a rate or daily limit. Back off and retry.
- `503 Service Unavailable` — Runway is shedding load. Retry with exponential backoff and jitter.
- `400 Bad Request` — your input is invalid. Never retry; fix the input.

The full matrix, with which codes are safe to retry, is on the [HTTP errors](../errors/http-errors.md) page.

**Validate inputs before sending them.** An oversized image or an unsupported codec in `promptImage` returns `400`. Test with a spread of real user inputs — odd aspect ratios, huge files, unusual containers — and check them against the [inputs reference](../assets/inputs.md). Every URL you pass must be HTTPS, resolve to a domain rather than an IP, answer HTTP HEAD, and return a correct `Content-Type`.

### 3. Secure your integration

**Never hard-code the key.** Load it from a secret manager or securely-set environment variables. Recommended stores: HashiCorp Vault, AWS Secrets Manager, Google Cloud Secret Manager, Azure Key Vault, Render environment variables, Heroku config vars.

Check your repository history for keys that are already committed:

```bash
git grep "key_"
```

If you find one — or find a key that was ever stored insecurely — disable it immediately from the API Keys tab and mint a replacement.

**Stop sharing keys.** Create keys liberally and revoke them when they are done. Staging gets its own key, separate from production. Every developer testing locally gets their own. Any key shared between people or environments should be disabled and replaced, because a shared key cannot be revoked without breaking everyone.

Remember that keys are org-scoped: removing a person from the organization does not revoke the keys they created.

### 4. Monitor your integration

Three metrics tell you almost everything:

- **API error rate.** Some errors are normal — a `404` on an idempotent delete, for example. A rising `429` rate means you are being throttled at a limit.
- **API request count per day.** This is your credit burn and your distance from the tier's daily cap.
- **Throttled task count.** A `THROTTLED` task is safe to treat as `PENDING` — it is stored but not yet enqueued. A lot of them means you are pressed against your concurrency limit and should tier up.

**Make sure you receive Runway's emails.** Autobilling charges and payment failures are announced by email to the address that registered the developer portal account. If those go to an unmonitored inbox or a spam folder, the first sign of a failed recharge will be an outage.

**Avoid account suspension.** Runway moderates unsafe requests, and repeated moderated requests lead to suspension. Confirm your use case is not in a moderated category, and add your own [content moderation](../errors/task-failures.md#safety-failures) upstream if user-supplied prompts reach the API.

<!-- /widget -->

## Quick pre-flight table

| Check | Where |
|---|---|
| Tier supports peak daily generations | [Usage tiers](../usage/tiers.md) |
| Autobilling configured and funded | [Autobilling](../usage/autobilling.md) |
| `429` / `503` handled with backoff + jitter | [HTTP errors](../errors/http-errors.md) |
| `failureCode` branching implemented | [Task failures](../errors/task-failures.md) |
| Inputs validated against size and codec limits | [Inputs](../assets/inputs.md) |
| Output URLs downloaded and re-hosted | [Outputs](../assets/outputs.md) |
| Key in a secret store, not in the repo | Section 3 above |
| Staging and production keys separated | Section 3 above |

## Related

- [Making API calls](./using-the-api.md) — the code this checklist protects
- [API reference](../endpoints/README.md) — every endpoint and parameter
- [Usage tiers](../usage/tiers.md) — concurrency, daily and monthly limits
- [Task failures](../errors/task-failures.md) — which failures refund credits

<!-- widget:cta -->

## Need higher limits than Tier 5?

Guaranteed concurrency, Slack support and custom terms come with an enterprise partnership.

[File an exception request](https://dev.runwayml.com/) · [Read the tier table](../usage/tiers.md)

<!-- /widget -->
