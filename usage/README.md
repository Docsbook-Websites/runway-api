---
title: Usage, Limits and Billing
description: How Runway's tiers govern concurrency and daily generations, and how autobilling keeps your credit balance from reaching zero during traffic.
---

# Usage and billing

Two mechanisms decide whether your integration keeps serving traffic: the tier that caps how much you can run, and the balance that pays for it.

<!-- widget:cards -->

- [Usage tiers](./tiers.md) — Concurrency, daily generations and monthly spend by tier {gauge}
- [Autobilling](./autobilling.md) — Automatic top-ups, and the three-strike failure schedule {refresh-cw}
- [Pricing](../pricing/README.md) — What each generation costs in credits {credit-card}
- [Go-live checklist](../get-started/go-live.md) — Getting both right before launch {clipboard-check}

<!-- /widget -->

## The short version

Every organization starts at **Tier 1**: one concurrent generation, 50 per day, $100 monthly spend cap. Tiers rise automatically the moment you cross a purchase threshold — $100 purchased puts you on Tier 3 (5 concurrent, 1,000/day) with no waiting period.

There is **no requests-per-minute limit**. Work beyond your concurrency is queued as `THROTTLED`, not rejected, so you do not need your own rate limiter. A persistent `429` means you have hit the **daily** cap instead.

Autobilling checks your balance hourly and recharges when it falls below a threshold you set, with a $10 minimum. Three consecutive payment failures stop it permanently until you fix the payment method and trigger a recharge manually.

## Related

- [HTTP errors](../errors/http-errors.md) — what a limit breach returns
- [Outputs](../assets/outputs.md) — why tasks show as `THROTTLED`

<!-- widget:cta -->

## Size your tier before launch

Estimate peak daily generations, then buy enough credits to land in the tier above it.

[Add credits](https://dev.runwayml.com/) · [Read the tier table](./tiers.md)

<!-- /widget -->
