# Next Session — TimkaMe v6

## Текущее состояние

v6 development session 04/21 — продакшен-готовый baseline:
- v5 frontend полностью портирован (PR #26-34): 150 файлов, 72 лендинговых страницы с unified content blocks, кот-маскот, gradient design
- Signup flow работает end-to-end: signup → Resend email с брендингом → click confirm → session → dashboard ✅
- Auth: login (email OTP + password), signup, email confirmation, /logout — все работает
- Resend SMTP + TimkaMe branded email templates задеплоены
- Dashboard audit + cleanup (PR #33): 6 рабочих страниц, 22 dead nav links удалены
- Public docs mirror: unnhost/timkame-docs-public
- Все 72 vertical landing pages используют unified content blocks (меняешь цену в одном месте — обновляется везде)

## Известные проблемы

1. **Logout button in sidebar non-functional** — /logout page works as workaround. Some v5 component on dashboard layout breaks JS. Low priority. Size S.

2. **Dashboard shows errors from v5 @ts-nocheck components** — analytics, billing, notifications, trial pages render but data queries fail (v5 schema). Sidebar items hidden but pages exist. P1.

3. **Password reset flow** — code in place, not tested end-to-end with real email. Should test next session.

4. **PR #25 webhook signature verification** — ready to merge, requires 24h logging-only rollout per spec before strict enforcement.

5. **Pricing model approved, not implemented** — two-tier ($39 Starter / $79 Professional) with pre-paid overage credits + trial protections. Size L, multiple PRs. Full prompt saved in Eugene's notes.

6. **Trial abuse protection not implemented** — Cloudflare Turnstile site key needed in Doppler. Eugene must create Turnstile site for v6.timkame.com first.

## Следующие задачи (priority order)

1. **Merge PR #25** (webhook signatures, 24h logging rollout) — S
2. **Test password reset flow** end-to-end with real email — S
3. **Fix dashboard logout button** + remaining @ts-nocheck cleanup — M
4. **Pricing v2 implementation** (two-tier + pre-paid credits + trial protection) — L, multi-PR
5. **Trial abuse protection** (after Turnstile keys in Doppler) — M

## Approved pricing model (для следующей сессии)

Starter: $39/mo, 150 min, $0.20/min overage
Professional: $79/mo, 300 min, $0.19/min overage (Most Popular)
Trial: 7 days, 15 min total, 3 calls/day, 3 min/call max, no overage

Overage: PRE-PAID credits only ($10/$25/$50/$100), never expire, refundable on cancel within 30d, optional auto-refill (opt-in)

Trial protection stack:
- Cloudflare Turnstile on signup
- 1 signup per IP per 24h
- disposable-email-domains block
- Global cap: 10 trial signups per day (waitlist when hit)
- Hard mid-call cutoff (current call completes, next blocked)
- Open-line abuse: silence detection, max 3 min/call, "Are you still there?" prompts

## Work Rule: Check v5 Reference First

When adding a new feature to v6, check `docs/v5-reference.md` first — v5 likely already solved the design problem (layout, states, edge cases, copy). Port visual patterns and UX decisions, not code.

## Блокеры

Нет.

---
Last updated: 2026-04-21T22:00:00-04:00
