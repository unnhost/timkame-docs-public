# Billing Lifecycle — TimkaMe v6

> Phase H.2 deliverable. Compiled 04/18/2026.

---

## Subscription Model

**Single tier:** $79/mo, 500 minutes included, $0.15/min overage.
**Trial:** 14 days, no CC required, 50 minutes.
**Billing cycle:** Monthly, anchored to signup date.

## Lifecycle States

```
[Trial] → [Active] → [Past Due] → [Suspended] → [Terminated]
                  ↑                                    ↓
                  └──── [Reactivated] ←────────────────┘ (within 30 days)
```

## Trial (14 days, no CC)

- Full access to all features
- 50 minutes of call handling
- No payment method required
- Day 10: reminder email + Telegram "Trial ends in 4 days"
- Day 13: final reminder "Trial ends tomorrow — add payment to continue"
- Day 14: trial expires → account locked (read-only, no calls handled)
- Day 14+: "Your trial has ended" page with payment CTA
- Day 30: if no conversion → account data deleted (per Privacy Policy)

## Active ($79/mo)

- Full access
- 500 minutes/month
- Usage tracked in real-time, visible in dashboard
- At 400 min (80%): Telegram alert "You've used 400 of 500 minutes this month"
- At 500 min: overage kicks in at $0.15/min, Telegram alert
- Invoice generated at cycle end: base ($79) + overage
- Stripe processes payment automatically

## Payment Failure → Past Due

**Stripe Smart Retries configured** (Stripe handles retry timing automatically).

### Dunning sequence (EN + RU, both sent)

| Day | Action | Channel |
|-----|--------|---------|
| 0 | Payment failed, retrying in 3 days | Email + Telegram |
| 3 | Second attempt failed, please update payment method | Email + Telegram |
| 7 | Third attempt failed, service suspended in 7 days | Email + Telegram + **Telegram to Eugene** (human outreach) |
| 14 | **Service suspended.** Calls forwarded to voicemail/busy. Account read-only. Resumes immediately after payment. | Email + Telegram |
| 30 | **Terminated.** Data retained 30 more days per Privacy Policy, then deleted. | Email + Telegram |

**14-day grace period** between first failure and suspension.

**Eugene Telegram at Day 7:** Human outreach often saves the customer. "Hey, noticed your payment didn't go through — can I help?" In Russian for Russian-speaking customers.

## Suspended

- Calls: forwarding disabled, callers hear busy signal or voicemail
- Dashboard: read-only, can update payment method
- Data: intact, not deleted
- Reactivation: instant upon successful payment
- Telegram notifications: still sent (so owner knows calls are being missed)

## Terminated (Day 30 after first failure, or voluntary cancellation)

- All services stopped
- Data retained 30 days (Privacy Policy requirement)
- After 30 days: transcripts deleted, recordings deleted, prompts deleted
- Account metadata (org name, email) retained for audit log
- Telnyx number released (or ported out if customer requests)
- Can reactivate within 30 days (data intact)
- After 30 days: new signup required

## Voluntary Cancellation

- Cancellation effective at end of current billing cycle (not immediate)
- Pro-rated credit for unused portion if requested
- Cancellation survey: mandatory dropdown (price / didn't work / quality / changed needs / competitor / other)
- Confirmation email with: last day of service, data retention timeline, reactivation instructions

## Refund Policy (in ToS day 1)

| Scenario | Refund |
|----------|--------|
| New signup within 14 days | Full refund, no questions asked |
| Mid-cycle cancellation | Pro-rated credit applied |
| Outage credit | 1 day credit per 4 hours confirmed downtime in a month |
| Usage >14 days | No retro refund for past usage |
| Processing time | 5-7 business days |

## Overage Billing

- Minutes calculated using 60/60 rule (round up to full minute)
- Calls <10 seconds: 0 minutes (not billed)
- 10-60 seconds: 1 minute
- 61-120 seconds: 2 minutes
- Overage calculated at end of billing cycle
- Single invoice: $79 base + (overage_minutes × $0.15)
- Overage alert sent at 500 min mark (real-time)

## Implementation Notes

### Stripe objects
- `Product`: "TimkaMe AI Receptionist"
- `Price`: $79/mo recurring
- `Subscription`: one per org
- `UsageRecord`: reported hourly (Stripe metered billing) or at cycle end
- `Invoice`: auto-generated, includes base + overage line items

### Database columns
- `organizations.subscription_status`: trial | active | past_due | suspended | terminated
- `organizations.trial_ends_at`: timestamp
- `organizations.current_period_end`: timestamp
- `organizations.minutes_used`: integer (reset each cycle)
- `organizations.stripe_customer_id`: string
- `organizations.stripe_subscription_id`: string
