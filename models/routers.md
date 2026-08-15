---
title: Model Routers — Automatic Model Selection
description: Let Runway pick the model per request based on your cost, latency or quality preference, with no routing fee and full reporting of what was used.
---

# Model routers

A model router chooses which model handles each request, based on the preference you configure — cost, latency or quality. There is **no additional cost** for routing.

## Why route

The catalogue has fourteen video models and nine image models, and the best choice shifts as new models ship. Hard-coding `gen4.5` today means revisiting your code every time something better or cheaper arrives.

A router moves that decision out of your codebase. You state the preference; Runway maps it to the best currently-available model.

## How billing works

Generations routed through a model router are billed at **the standard rate of whichever model the router selects**. There is no routing markup.

The response metadata reports both the model that was used and the realized cost in credits, so per-request accounting stays exact even though the model varies.

This matters for cost modelling: a cost-optimised router will usually land on cheaper models, but the price of any single generation is only known after it runs. Read the realized cost from the response rather than assuming a fixed per-request figure.

## When not to route

Routing is the wrong tool when the model itself is part of your product's behaviour:

- You depend on a capability only one model has — 30-second output (`seedance2_5`), video editing (`aleph2`), native audio (`veo3.1`), character performance (`act_two`).
- You need reproducibility across runs, where a changed model would change the look.
- You have tuned prompts to one model's quirks.

In those cases, name the model explicitly.

## Related

- [Models](./README.md) — the full catalogue a router picks from
- [Pricing](../pricing/README.md) — the standard rates routed requests are billed at
- [API reference](../api/README.md) — router configuration parameters

<!-- widget:cta -->

## Stop maintaining a model matrix

Set a preference once and let routing track the catalogue for you.

[Create your account](https://dev.runwayml.com/) · [Compare models manually](./README.md)

<!-- /widget -->
