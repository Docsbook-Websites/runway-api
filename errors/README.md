---
title: Errors — HTTP Codes and Task Failures
description: Two kinds of failure exist in the Runway API — rejected requests and failed tasks. They have different codes and different retry rules.
---

# Errors

Failures come in two shapes, and confusing them leads to retrying things that will never succeed.

<!-- widget:cards -->

- [HTTP errors](./http-errors.md) — The request was rejected. Status codes and retry safety {triangle-alert}
- [Task failures](./task-failures.md) — The request was accepted but generation failed. `failureCode` values {circle-x}

<!-- /widget -->

## Telling them apart

An **HTTP error** means the request never became a task. You get a `4xx` or `5xx` status immediately, and nothing was charged.

A **task failure** means the request was accepted with a `200` and a task ID, then failed during processing. You discover it by retrieving the task and reading `failureCode`.

## The two rules worth memorising

**`429` means the daily cap, not requests per minute.** There is no per-minute limit — excess concurrent work is queued as `THROTTLED`. A persistent `429` means tiering up, not backing off harder.

**`SAFETY.INPUT.*` is the one failure that does not refund credits.** Never retry it: you will be charged again for the same rejection.

## Related

- [Go-live checklist](../get-started/go-live.md) — testing failure paths before launch
- [Usage tiers](../usage/tiers.md) — the limits behind most `429`s
- [Inputs](../assets/inputs.md) — the constraints behind most `400`s

<!-- widget:cta -->

## Handle failures before users see them

The go-live checklist walks every failure path with the code to handle it.

[Read the checklist](../get-started/go-live.md) · [Create your account](https://dev.runwayml.com/)

<!-- /widget -->
