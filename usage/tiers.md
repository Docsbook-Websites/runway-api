---
title: Usage Tiers — Concurrency, Daily and Monthly Limits
description: Five tiers govern how many generations you can run at once and per day. Tiers rise automatically as you purchase credits, with no waiting period.
---

# Usage tiers

Limits protect the API against abuse and keep access fair. Every organization sits in a tier, and the tier sets three numbers: how many generations run at once, how many run per day, and how much you can spend per month.

Limits are **per organization**, and shared across all models within a modality.

## The tiers

| Tier | Max concurrency | Max generations/day | Max spend/month | How to reach it |
|---|---|---|---|---|
| 1 | 1 | 50 | $100 | Default |
| 2 | 3 | 500 | $500 | Immediately after $50 purchased |
| 3 | 5 | 1,000 | $2,000 | Immediately after $100 purchased |
| 4 | 10 | 5,000 | $20,000 | Immediately after $1,000 purchased |
| 5 | 20 | 25,000 | $100,000 | Immediately after $5,000 purchased |

When your organization reaches a tier's spend criterion, **the upgrade applies automatically with no waiting period**.

All models within the same modality share these numbers. On Tier 3 you can run 5 concurrent Gen-4.5 generations *and* 5 concurrent Veo 3.1 generations at the same time — video models share one pool, image models another. Runway Characters shares the video pool.

## Concurrency

Concurrency is the number of tasks the API will process for you simultaneously. Submit more and the extras get status `THROTTLED` — stored on Runway's servers but not yet enqueued. Throttled tasks enter the queue roughly in submission order.

**Treat `THROTTLED` as `PENDING`.** It is not an error, and nothing is lost.

### You do not need to rate-limit your own requests

There is **no maximum requests-per-minute limit**, as long as you stay inside your daily generation cap. Submitting more work than your concurrency allows simply queues it. Do not build rate-limiting logic for this — Runway handles the queueing.

**Worked example.** You want 200 videos and your concurrency limit is 5:

1. Submit all 200 tasks at once.
2. Runway executes them 5 at a time, in creation order.
3. The first 5 finish in ~15 seconds; videos 6–10 start.
4. All 200 complete in roughly 10 minutes — `15 seconds × 200 / 5`.

Under heavy system load you may occasionally see less than your maximum concurrency. If you need a guaranteed minimum, use the limits exception form on the usage page of the developer portal.

## Daily generations

The daily cap applies to a **rolling 24-hour window**. It resets continuously based on when each request was made, not at a fixed time of day.

Exceeding it returns `429 Too Many Requests` on task creation. Since there is no per-minute limit, a persistent `429` means you are against the daily cap — the fix is tiering up, not backing off.

## Monthly spend

This caps credit purchases in a rolling 30-day window. You cannot buy past it, and [autobilling](./autobilling.md) recharges are capped at your remaining monthly spend.

If your remaining monthly spend drops below $10, autobilling fails outright — because $10 is the minimum recharge.

## Going beyond Tier 5

For custom tiers, higher limits or guaranteed concurrency, file an exception request from the usage page while logged in to the developer portal. These fall under enterprise partnerships, which also include:

- Faster support via Slack or email
- Earliest access to new features
- Direct product feedback and feature requests
- Implementation and usage guidance
- Custom payment terms

For custom concurrency options, contact enterprise@runwayml.com.

## Related

- [Autobilling](./autobilling.md) — automatic recharge, and how tiers cap it
- [Pricing](../pricing/README.md) — what your daily allowance costs
- [Go-live checklist](../get-started/go-live.md) — tiering up before launch

<!-- widget:cta -->

## Need more than 20 concurrent generations?

Guaranteed concurrency and custom limits come with an enterprise partnership.

[File an exception request](https://dev.runwayml.com/) · [Read the go-live checklist](../get-started/go-live.md)

<!-- /widget -->
