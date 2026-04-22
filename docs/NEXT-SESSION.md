# Next Session — TimkaMe v6

## Текущее состояние

v6 development session 04/22 — pricing v2 PR A merged:
- v5 frontend полностью портирован (PR #26-34): 150 файлов, 72 лендинговых страницы с unified content blocks, кот-маскот, gradient design
- Signup flow работает end-to-end: signup → Resend email с брендингом → click confirm → session → dashboard ✅
- Auth: login (email OTP + password), signup, email confirmation, /logout — все работает
- Resend SMTP + TimkaMe branded email templates задеплоены
- Dashboard audit + cleanup (PR #33): 6 рабочих страниц, 22 dead nav links удалены
- Public docs mirror: unnhost/timkame-docs-public
- Все 72 vertical landing pages используют unified content blocks (меняешь цену в одном месте — обновляется везде)
- **Pricing v2 PR A merged**: two-tier model ($39 Starter / $79 Professional), schema ready (plan_tier, overage_transactions, trial_usage_daily), all 72 landing pages updated, DEC-029/030/031 added. No runtime behavior changes yet.

## Известные проблемы

1. **Logout button in sidebar non-functional** — /logout page works as workaround. Some v5 component on dashboard layout breaks JS. Low priority. Size S.

2. **Dashboard shows errors from v5 @ts-nocheck components** — analytics, billing, notifications, trial pages render but data queries fail (v5 schema). Sidebar items hidden but pages exist. P1.

3. **Password reset flow** — code in place, not tested end-to-end with real email. Should test next session.

4. **PR #25 webhook signature verification** — ready to merge, requires 24h logging-only rollout per spec before strict enforcement.

5. **Pricing v2 PR B pending** — trial protection + signup guards (Turnstile, disposable email block, IP rate limit, daily cap). Requires Turnstile keys in Doppler first.

6. **Pricing v2 PR C pending** — overage credits + Stripe checkout + dashboard billing UI. Requires Stripe test keys in Doppler.

## Следующие задачи (priority order)

1. **Merge PR #25** (webhook signatures, 24h logging rollout) — S
2. **Test password reset flow** end-to-end with real email — S
3. **Fix dashboard logout button** + remaining @ts-nocheck cleanup — M
4. **Pricing v2 PR B** (trial protection + signup guards) — M, requires Turnstile keys
5. **Pricing v2 PR C** (overage + Stripe + dashboard billing) — L, requires Stripe test keys

## Pricing v2 status

PR A ✅ merged — schema + content + 72 landing pages (DEC-029/030/031)
PR B ⏳ pending — trial protection + signup guards (needs Turnstile keys in Doppler)
PR C ⏳ pending — overage credits + Stripe + dashboard billing (needs Stripe test keys)

## Work Rule: Check v5 Reference First

When adding a new feature to v6, check `docs/v5-reference.md` first — v5 likely already solved the design problem (layout, states, edge cases, copy). Port visual patterns and UX decisions, not code.

## Блокеры

Нет.

---
Last updated: 2026-04-22T10:00:00-04:00
