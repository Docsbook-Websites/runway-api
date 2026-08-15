---
title: API Setup — Organization, Keys and Credits
description: Create a Runway organization, mint your first API key, and add the credits that unlock generation. Five minutes from signup to a working request.
---

# Account setup

An **organization** is the unit that owns everything: API keys, credits, usage tier and billing. One organization usually corresponds to one integration.

<!-- widget:stepper -->

### Sign up and create an organization

Sign up in the [developer portal](https://dev.runwayml.com/). After signing up you are offered the option to create a new organization — take it. Resources like API keys and configuration live inside it.

### Create an API key

Open the **API Keys** tab and create a key. Give it a descriptive name — `checkout-service-staging` rather than `key 2` — so you know what breaks when you revoke it later.

The key is shown **once**. Copy it straight into a password manager or secret store. Runway never returns the key in plaintext again; lose it and you disable it and mint a new one.

### Add credits

Generations are paid in credits, at **$0.01 per credit**. Open the **Billing** tab and make your first purchase — the minimum is **$10** (1,000 credits). Sales tax may apply depending on your location.

Until the organization has credits, generation requests will not run.

### Make your first request

With an organization, a key and a balance, you are ready to call the API. Continue to [making API calls](./using-the-api.md).

<!-- /widget -->

## Using your key

Requests authenticate with your key in the request headers. Both SDKs pick it up automatically from the `RUNWAYML_API_SECRET` environment variable, so you rarely pass it explicitly.

For local testing:

```bash
export RUNWAYML_API_SECRET="key_123456789012345678901234567890"
```

```js
import RunwayML from '@runwayml/sdk';

// Reads RUNWAYML_API_SECRET from the environment
const client = new RunwayML();
```

In production, never hard-code the key. Load it from a secret manager or a securely-set environment variable — see [securing your integration](./go-live.md#3-secure-your-integration) for the specifics and a `git grep` check that catches keys already committed.

## Key access is org-scoped, not user-scoped

Removing someone from your organization **does not** revoke their API key access. Keys belong to the organization, not to the person who created them.

To fully cut off access when someone leaves, disable their keys separately from the API Keys tab. Treat this as part of your offboarding checklist.

## Related

- [Making API calls](./using-the-api.md) — your first generation, with code
- [Go-live checklist](./go-live.md) — key hygiene, monitoring and failure handling
- [Autobilling](../usage/autobilling.md) — keep the balance topped up automatically
- [Usage tiers](../usage/tiers.md) — how purchases raise your concurrency and daily limits

<!-- widget:cta -->

## Get your key

Organization, key and a $10 credit purchase — then the quickstart runs.

[Open the developer portal](https://dev.runwayml.com/) · [Make your first call](./using-the-api.md)

<!-- /widget -->
