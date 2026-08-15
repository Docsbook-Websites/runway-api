---
title: Autobilling — Automatic Credit Top-Ups
description: Set a balance threshold and a recharge amount so your integration never runs out of credits mid-traffic. Includes the retry schedule when a payment fails.
---

# Autobilling

Running out of credits stops generation. Autobilling tops your balance up automatically so it does not happen mid-launch.

## Setting it up

<!-- widget:stepper -->

### Open the billing tab

In the developer portal, go to your organization's **Billing** tab and choose to set up autobilling.

### Choose your thresholds

**Recharge below** — the credit balance at which a top-up should trigger.

**Recharge amount** — how many credits to buy. This must be at least **1,000 credits ($10)**.

Set "recharge below" high enough to cover the traffic you serve in an hour, since the check runs hourly. If a peak hour burns 800 credits, a threshold of 200 is too low.

### Add a payment method

Add a card through Stripe. Sales tax may apply to charges depending on your location.

<!-- /widget -->

## How the recharge runs

Every hour, Runway checks whether your balance has fallen below the "recharge below" threshold. If it has, the payment method on file is charged for the "recharge amount".

Because the check is hourly rather than instantaneous, a sharp traffic spike can drain the balance between checks. Size the threshold against your peak hourly burn, not your average.

## When a payment fails

The retry schedule is fixed and finite:

| Attempt | When | On failure |
|---|---|---|
| 1 | At the hourly check | Email notification, retry in 24 hours |
| 2 | 24 hours later | Email notification, retry in 24 hours |
| 3 | 24 hours later | Email notification, **autobilling stops** |

After three consecutive failures, **Runway will not reattempt autobilling.** Your balance then drains to zero and generation stops.

Every notification goes to the email address that registered the developer portal account. If that inbox is unmonitored or Runway's mail lands in spam, the first sign of a problem will be an outage — so confirm delivery as part of your [go-live checklist](../get-started/go-live.md).

## Fixing a stopped autobilling

Whether you need a new payment method depends on why the charge failed. Insufficient funds may clear on its own; an expired card will not.

After updating payment details, the portal offers a **manual autobilling trigger**. If that manual payment succeeds, autobilling that had stopped after three failures is re-enabled.

## Interaction with usage tiers

Each [usage tier](./tiers.md) caps credit purchases in a rolling 30-day window. Two consequences:

- If your remaining monthly spend is **lower than your recharge amount**, the recharge is capped at the remainder.
- If your remaining monthly spend is **below $10**, autobilling fails — $10 is the minimum recharge.

An organization pressed against its monthly cap therefore loses autobilling before it loses purchasing. If you expect to approach the cap, tier up rather than relying on top-ups.

## Related

- [Usage tiers](./tiers.md) — the monthly spend caps that bound recharges
- [Pricing](../pricing/README.md) — sizing the threshold against real costs
- [Go-live checklist](../get-started/go-live.md) — confirming email delivery

<!-- widget:cta -->

## Don't run dry mid-launch

Autobilling takes two minutes to configure and removes a whole class of outage.

[Open the billing tab](https://dev.runwayml.com/) · [Read the go-live checklist](../get-started/go-live.md)

<!-- /widget -->
