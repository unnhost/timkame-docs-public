# Support Operations — TimkaMe v6

> Phase H.4 deliverable. Compiled 04/18/2026.

---

## SLA (published, honest)

> "TimkaMe is a growing business supported by its founder. We prioritize reliability over 24/7 coverage."

| Priority | Response Time | Channel |
|----------|-------------|---------|
| P0 — Service down (calls not handling) | 4 hours target | Telegram + SMS to Eugene |
| P1 — Major feature broken (billing, dashboard) | 24 business hours | Telegram, Email |
| P2 — Standard (how-to, feature request, minor bug) | 24 business hours (Mon-Fri) | Email, Telegram |
| Weekend | Best-effort | Telegram only |

Business hours: Mon-Fri, 9 AM – 6 PM EST.

## Support Stack (<$100/mo total)

| Tool | Cost | Purpose |
|------|------|---------|
| **Telegram @TimkaSupport** | Free | Primary support channel. Bilingual FAQ bot. |
| **support@timkame.com** | Free (via Resend) | Email fallback. Forwarded to Eugene's inbox. |
| **Crisp or Plain** (free tier) | $0 | Chat widget on dashboard (optional). |
| **Upptime** (GitHub Actions) | $0 | Status page at status.timkame.com |
| **Public FAQ** | $0 | EN + RU, top 20 questions, on website |

**Total support tooling cost: $0/mo**

## Telegram Support Bot (@TimkaSupport)

### Bilingual FAQ auto-responses
Bot recognizes keywords and responds in the same language the customer writes:

**Russian triggers:**
- "не работает" → troubleshooting steps
- "оплата" / "счёт" → billing FAQ
- "отменить" → cancellation process link
- "номер" / "переадресация" → forwarding setup guide

**English triggers:**
- "not working" → troubleshooting steps
- "billing" / "payment" → billing FAQ
- "cancel" → cancellation process link
- "forwarding" / "number" → setup guide

### Escalation to Eugene
If bot can't resolve → message forwarded to Eugene's personal Telegram with context (org name, subscription status, last 3 messages).

## Escalation Path

```
Customer → Telegram bot (auto FAQ)
    ↓ (unresolved)
Eugene via Telegram (manual response)
    ↓ (technical issue)
Check Sentry/logs → fix → deploy → respond
    ↓ (P0 outage)
Incident runbook → status page update → fix → postmortem
```

## P0 Incident Response

1. Telegram + SMS alert received
2. Eugene acknowledges within 15 min (if awake)
3. Status page updated: "Investigating"
4. Auto-create incident file: `docs/incidents/YYYY-MM-DD-summary.md`
5. Diagnose → fix → deploy
6. Status page: "Resolved"
7. Within 24h: postmortem in incident file
8. Affected customers: personal Telegram message with explanation

## First 10 Customers — Concierge Support

For the first 10 paying customers:
- Eugene's personal phone number (direct line)
- Setup call within 24h of signup (Eugene walks through everything)
- Day 1/3/7/30 follow-up messages via Telegram
- Feedback collected at Day 7 and Day 30
- Day 30: reference request (case study permission)

This does not scale. It's intentional. First 10 customers get white-glove treatment to learn what breaks.

## Knowledge Base (FAQ)

### Top 20 questions (EN + RU, separately authored)

**Setup:**
1. How do I set up call forwarding?
2. How long does it take to start?
3. Can I keep my existing number?
4. What carriers are supported?

**Calls:**
5. What happens when the AI can't understand?
6. Can the AI transfer to me?
7. What if I get a call while the AI is handling one?
8. Are calls recorded?

**Billing:**
9. How does billing work?
10. What happens if I go over 500 minutes?
11. Can I cancel anytime?
12. Do you offer refunds?

**Privacy/Security:**
13. Is my data safe?
14. Can I export my data?
15. How long are recordings kept?
16. Do you sell my data?

**Troubleshooting:**
17. My calls aren't being answered by the AI
18. The AI said something wrong
19. My notification didn't arrive
20. How do I change my greeting?

### FAQ format
Each answer: 2-3 sentences max. Link to detailed doc if needed. No jargon. Both EN + RU available, Russian separately authored (not translated).

## Churn Prevention

### Signals to watch (PostHog)
- No calls received in 7 days (forwarding might be broken)
- Dashboard not visited in 14 days
- Usage <10% of included minutes (customer may not be getting value)
- Failed payment (dunning kicks in)

### Eugene's actions
- Low-usage alert (Telegram to Eugene) → personal outreach: "Hey, noticed your AI hasn't received calls this week — is forwarding still set up?"
- Churn survey results reviewed weekly
- Top churn reasons → product fixes or FAQ additions
