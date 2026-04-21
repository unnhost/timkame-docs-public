# Implementation Roadmap — TimkaMe v6

> Эстимейты убраны. С Claude Code скорость непредсказуемо высокая. Фокус на порядке задач и зависимостях, не на часах.

## Краткое резюме (RU)

Дорожная карта от текущего состояния до первого платящего клиента.

**Ключевые фазы:**
- **Phase 0** — EIN, GitHub repo, Telnyx аккаунт, Stripe test mode, Doppler
- **Phase 1** — основная сборка: scaffolding → телефония → фичи → биллинг → safeguards
- **Phase 2** — лэндинги EN + /ru, шаблоны, FAQ, SEO, мониторинг
- **Phase 0.5** (7 недель календарных) — LLC, страховка, Stripe live. Запускается когда продукт готов к демо + есть 3 тёплых лида.

**Критический путь:** A.4 Voice Gate → Phase 0 → Phase 1 → Phase 2 → QA → Phase 0.5 → первый клиент.

**Кратчайший путь:** Phase 0.5 параллельно Phase 2.

---

## Timeline Summary

| Phase | Sequence | Parallel? |
|-------|----------|-----------|
| Phase 0 (Technical Infra) | First | Yes, during A–E |
| Phase A–E (Research + Planning) | First | ✅ DONE |
| A.4 Voice Gate | Before Phase 1 | ✅ DONE |
| Phase 1 (Core Build) | After Phase 0 + A.4 | No |
| Phase 2 (Polish) | After Phase 1 | No |
| Phase 0.5 (Pre-Launch) | 7 weeks calendar | Triggered when demo-ready |
| Phase 4 (Post-Launch) | After Phase 0.5 | No |

---

## Phase 0 — Technical Infrastructure ✅ DONE

Eugene does these.

| Task | Dependency |
|------|-----------|
| EIN (sole proprietor, online) | None |
| GitHub repo + CI pipeline | None |
| Doppler secrets setup | None |
| Telnyx account + first number | EIN |
| Stripe test mode setup | EIN |

DNS + Cloudflare already configured. DMARC/SPF/DKIM during Phase 1 email setup.

---

## Phase 1 — Core Product Build

### Scaffolding ✅ DONE

| Task | Size | Notes |
|------|------|-------|
| Monorepo setup (Turborepo) | S | apps/web, apps/server, packages/shared, packages/db |
| TypeScript strict + ESLint + 400-line rule | S | All linting rules from coding-standards.md |
| Supabase schema: organizations, user_profiles | S | From C.3 data model |
| RLS policies on all tables | S | Cross-tenant test in CI |
| Supabase Auth integration | S | Email + Google login |
| CI pipeline (typecheck + lint + test + build) | S | GitHub Actions |
| Hetzner US server setup | S | CPX21 Ashburn, Node.js, PM2 |
| Hono backend skeleton | S | Health check, middleware, error handling |
| Next.js app skeleton | S | App Router, layouts, auth guard |
| Doppler integration (server + CI) | S | All secrets from Doppler |
| Deploy pipeline (GitHub Actions → Hetzner) | S | git push → build → deploy → health check |

### Telephony + Voice (in progress)

| Task | Size | Notes |
|------|------|-------|
| Telnyx SIP → OpenAI Realtime integration | M | ✅ Done |
| Call webhook handler (Hono) | S | ✅ Done |
| Transcript storage + display | S | ✅ Done |
| AI greeting with recording disclosure | S | ✅ Done |
| Smart transfer to owner | M | 20-sec ring, fallback to message |
| Concurrent call handling | S | Separate AI sessions per call |
| Call quality metrics collection | S | first_response_ms, turn_latency_ms |
| Billable minutes calculation | S | 60/60 rule, <10 sec = 0 |

### Core Features (in progress)

| Task | Size | Notes |
|------|------|-------|
| Call forwarding wizard (4 carrier tracks) | M | Verizon, AT&T, T-Mobile, VoIP — EN + RU instructions |
| Number purchase flow (Telnyx API) | S | Search + buy NY area code |
| Prompt editor | M | ✅ Done (PR #19, org-level columns) |
| PHI scanner (regex + LLM validation) | S | Block DOB/SSN/medical in prompts |
| Telegram notification integration | S | ✅ Done |
| Email notification fallback (Resend) | S | If no Telegram configured |
| 10DLC registration automation | S | Telnyx ISV API |
| DMARC/SPF/DKIM setup | S | Resend + Cloudflare DNS |

### Billing + Dashboard

| Task | Size | Notes |
|------|------|-------|
| Stripe integration (test mode) | M | Product, Price, Subscription, Webhooks |
| Usage metering | S | Minutes tracking, 80% alert, overage |
| Trial lifecycle | S | 14-day countdown, reminders, lock |
| Dunning sequence | S | Day 0/3/7/14/30 emails + Telegram |
| Admin dashboard — main page | M | Calls today, minutes used, recent activity |
| Call history page | S | List + detail view with transcript |
| Settings page | S | Greeting, transfer config, Telegram, language |
| Port existing UI designs | M | Screenshot → Tailwind components |

### Safeguards + Testing

| Task | Size | Notes |
|------|------|-------|
| Outbound rate limits (20/day) | S | Per-org, per-number |
| Outbound 24h window enforcement | S | Only reply to inbound |
| Content moderation on bot outputs | S | Before TTS |
| Warm-up schedule enforcement | S | 28-day ramp for new numbers |
| Telnyx spend caps per-org | S | API-level |
| CNAM registration automation | S | On number provision |
| Integration tests — critical paths | M | Call flow, signup, billing, RLS |
| QA scenarios 1-5 manual testing | S | Real phone calls |
| Sentry integration | S | Backend + frontend |

---

## Phase 2 — Polish

| Task | Size | Notes |
|------|------|-------|
| Onboarding analytics (drop-off tracking) | S | PostHog events at each step |
| Prompt templates — 5 verticals × 2 languages | M | Auto repair, dental, moving, legal, home services |
| Help center / FAQ page (EN + RU) | M | Top 20 questions, separately authored |
| Founder admin tools | M | Impersonation, audit log viewer, overrides |
| Landing page — English (/) | M | From content blocks, SEO optimized |
| Landing page — Russian (/ru) | M | Separately authored, not translated |
| SEO basics + hreflang + sitemap | S | Dual-language sitemap |
| Legal docs live on site | S | ToS, Privacy, AUP — EN + RU pages |
| QA scenarios 6-10 manual testing | S | Real phone calls |
| Error handling cleanup | S | Edge cases, error messages |
| Status page (Upptime) | S | Setup + monitors |
| Synthetic test calls | S | Cron job, alerting |
| Referral program (Stripe coupons) | S | $20/$20, unique codes |

---

## Phase 0.5 — Pre-Launch Sequence (7 weeks calendar)

**Triggered when:** All 10 QA pass + 3 warm prospects + product production-worthy.

Eugene handles this. ~10 hours of Eugene's time spread over 7 weeks (mostly waiting for LLC publication).

See `docs/phase-0.5-prelaunch-checklist.md` for full checklist.

---

## Phase 4 — Post-Launch Validation

| Week | Activity |
|------|----------|
| 1 | 10 warm outreach (personal network, Telegram groups) |
| 2 | 3 Russian FB group posts + Brighton community |
| 3 | 10 Brighton walk-ins with live demo on phone |
| 4 | Tally results, assess vs pre-commitments |
| 5-8 | Iterate on first customer feedback |

---

## Critical Path

```
A.4 Voice Gate ✅
    ↓
Phase 0 Technical ✅
    ↓
Phase 1 Core Build (in progress)
    ↓
Phase 2 Polish
    ↓
QA Scenarios Pass
    ↓
Phase 0.5 Pre-Launch (7 weeks calendar) — can overlap with Phase 2
    ↓
First Customer
```

---

## Size Guide

| Size | Meaning |
|------|---------|
| **S** | One session — single focused task |
| **M** | Few sessions — multiple components or integration work |
| **L** | Break down further before starting |

---

## Risk Register

| Risk | Impact | Mitigation |
|------|--------|-----------|
| A.4 voice gate fails | Blocks entire voice pipeline | H.1 defines pivot options. ✅ Resolved — gate passed. |
| Scope creep | Delays everything | AI rejection log, feature freeze, weekly review |
| Telnyx SIP integration harder than expected | +delay | Fallback: Telnyx WebSocket SDK (more code, still works) |
| Supabase free tier limits hit | Need $25/mo upgrade | Revenue covers this at 1 customer |
| Eugene's schedule changes | Calendar slips | Pre-commitments.md: honest pause OK, silent abandonment not OK |
