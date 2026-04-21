# Product Analytics — TimkaMe v6

> Phase H.6 deliverable. Compiled 04/18/2026.

---

## Tool: PostHog (free tier)

- **Limit:** 1M events/month (sufficient for <100 customers)
- **Session recording:** 5K sessions/month (free tier)
- **Feature flags:** included
- **Cost:** $0/mo at our scale

## Metrics Tracked from Day 1

### Activation funnel

| Step | Event | Target |
|------|-------|--------|
| 1. Visit landing | `page_view` (landing) | — |
| 2. Click signup | `signup_click` | >5% of visitors |
| 3. Complete signup | `signup_complete` | >60% of clicks |
| 4. Set up forwarding | `forwarding_configured` | >70% of signups |
| 5. First test call | `first_test_call` | >80% of forwarding setups |
| 6. First real call | `first_real_call` | >50% of test callers |
| 7. Payment added | `payment_added` | >30% of trial users |

**Key metric: Time to first value** = time from `signup_complete` to `first_real_call`. Target: <24 hours.

### Usage metrics

| Metric | How measured | Alert threshold |
|--------|-------------|----------------|
| Minutes used / included | Stripe metered billing | Alert at 80% |
| Calls per day per tenant | `call_completed` events | <1 call/day for 7 days → churn risk |
| Avg call duration | Call records | >5 min avg = unusual |
| Transfer rate | `call_transferred` / `call_completed` | >50% = AI not helping enough |
| Transcript accuracy | Manual spot-check (weekly) | — |
| Notification delivery | `notification_sent` + `notification_delivered` | <95% delivery → investigate |

### Feature adoption

| Feature | Event | Healthy signal |
|---------|-------|---------------|
| Prompt editor | `prompt_edited` | ≥1 edit in first 7 days |
| Smart transfer | `transfer_configured` | ≥1 in first 3 days |
| Call history | `call_history_viewed` | Weekly usage |
| Export | `data_exported` | Low — monitor for demand |

### Retention & churn

| Metric | How | Action |
|--------|-----|--------|
| **Churn rate** | Monthly: lost / total | Target <5% |
| **Churn reason** | Mandatory dropdown at cancellation | Weekly review |
| **NPS** | In-app survey at Day 60 paying | Target >40 |
| **Language preference** | % EN landing vs RU, % app language | Validate target market |

### Churn reason dropdown options
1. Too expensive
2. AI didn't work well enough
3. Voice quality not good enough
4. Changed business needs
5. Switched to competitor
6. Other (free text)

## Dashboard

Eugene's weekly review dashboard (PostHog):
1. New signups this week
2. Trial → paid conversion this week
3. Active tenants (received ≥1 call this week)
4. Churn events this week + reasons
5. Avg time to first value
6. Language preference split (EN vs RU)

## Privacy Compliance

- PostHog self-hosted option available if data residency becomes an issue
- No PII in event properties (org_id, not org_name)
- Phone numbers never sent to PostHog
- Session recordings only on dashboard, not landing page
- Consent banner for cookies/tracking on landing page
