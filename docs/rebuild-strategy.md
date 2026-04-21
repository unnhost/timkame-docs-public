# Rebuild Strategy — TimkaMe v6

> Phase D deliverable. Compiled 04/18/2026.

## Краткое резюме (RU)

Стратегия перестройки: полный rewrite с сохранением дизайна frontend.

**Выбор:** Полный rewrite (новый репозиторий, новый backend, портируем UI-дизайн).

**Почему НЕ refactor:**
- server.js — 2393 строки, 10 смешанных ответственностей, невозможно отрефакторить — только переписать
- 22 API endpoint без валидации, 170 типов `any`, 0% тестов — фундамент слишком слабый
- Переход с Twilio на Telnyx = переписывание всего голосового слоя в любом случае
- 0 пользователей = 0 стоимость миграции. Единственный момент в жизни продукта когда rewrite бесплатен

**Почему НЕ strangler fig:** нет работающего production чтобы защищать. Strangler fig в 1.5-2x медленнее чем rewrite при том же результате.

**Что сохраняем:** дизайн admin panel, маркетинговый дизайн, проверенные промпты, контент.
**Что выбрасываем:** весь backend, DB schema, n8n workflows, deployment скрипты.

**Оценка:** 150-200 часов Phase 1 + 50-70 часов Phase 2 = 8-14 недель при 20 ч/нед.

---

## D.1 — Honest Analysis of Options

### Option 1: Full Rewrite with Frontend Preservation

**What it means:** New GitHub repo. New backend from scratch. Port admin panel UI designs (layouts, components, visual design) into new Next.js project with proper structure. Discard all backend code, DB schema, n8n workflows. Rebuild voice pipeline on Telnyx.

**Hour estimate at 20 hrs/week:** 150-200 hrs = 8-10 weeks for Phase 1 core. +50-70 hrs (3-4 weeks) for Phase 2 polish. Total: ~11-14 weeks.

**Pros:**
- Clean foundation with standards enforced from commit 1
- No legacy debt carried forward
- Learning maximized (Eugene's primary goal)
- Zero users = zero migration cost
- Can adopt Telnyx from day 1 (no Twilio migration mid-flight)

**Cons:**
- 11-14 weeks before feature parity with current (broken) system
- Risk of scope creep during rebuild
- Admin panel UI must be manually ported (not copy-paste — structure changes)

### Option 2: Strangler Fig

**What it means:** Keep current system running. Build new modules alongside. Route new functionality to new code. Gradually replace old modules until nothing references old code.

**Hour estimate:** 200-300 hrs = 10-15 weeks. 1.5-2x slower than rewrite because you maintain two systems simultaneously.

**Pros:**
- Incremental progress visible immediately
- Can ship partial improvements while rebuilding
- Lower risk of "big bang" failure

**Cons:**
- Must maintain Twilio integration while building Telnyx (two telephony systems)
- Old patterns leak into new code (seen this happen in every strangler project)
- 22 unvalidated API routes remain live during transition
- n8n workflows can't be gradually strangled — they're all-or-nothing per workflow
- 2x slower for same result

**Why it loses:** With zero users, there's no running production to protect. Strangler fig exists to avoid downtime during migration. We have no uptime to protect.

### Option 3: Refactor In Place

**What it means:** Keep current repo. Fix server.js by extracting modules. Add Zod validation to 22 routes. Add tests. Split 18 files >1000 lines. Replace n8n gradually.

**Hour estimate:** 150-250 hrs = 8-13 weeks. Similar calendar time to rewrite, worse result.

**Pros:**
- Familiar codebase
- No cold-start on project structure

**Cons:**
- 170 `any` types must be individually typed
- server.js extraction means rewriting it anyway
- 18 files >1000 lines each need splitting — effectively rewriting them
- Twilio → Telnyx migration is still a full rewrite of telephony layer
- n8n → code migration is still a full rewrite of workflow layer
- Old patterns persist in files you don't touch — inconsistency compounds

**Why it loses:** When you need to rewrite server.js, the telephony layer, and all workflows, the amount of "kept" code is too small to justify maintaining the old structure. You're doing 70% of a rewrite with 100% of the legacy baggage.

---

## D.2 — Recommendation

**Full rewrite with frontend preservation.**

### Top 3 reasons:

1. **Zero users.** This is the only moment in a product's life where rewrite cost = 0 migration risk. Every day with users makes this harder.

2. **Foundation too broken to patch.** server.js (10 concerns), 22 unvalidated routes, 0% tests, 170 `any` types, PII in logs. Patching each individually takes the same time as rebuilding correctly.

3. **Telephony provider change (Twilio → Telnyx) requires rewrite of voice layer anyway.** The biggest, most complex part of the backend — voice call handling — can't be refactored, it must be rebuilt for Telnyx APIs. Once you accept that, the incremental cost of rewriting the rest (auth, billing, notifications) is small.

### Top 2 risks + mitigation:

1. **Scope creep.** Solo founder + AI assistants = "while I'm here, let me also add..." Mitigation: Phase E coding standards with AI rejection log. Feature freeze enforced. Every AI suggestion outside approved scope → logged and rejected.

2. **Voice quality gate blocks everything.** If Russian voice fails A.4, the entire voice pipeline design is wrong. Mitigation: Do A.4 BEFORE writing voice code. Run the blind listening test first. If it fails, stop and reassess — don't build on hope.

### What would change this answer:
- If we had 50+ paying customers → strangler fig (can't afford downtime)
- If server.js were <500 lines with clean separation → refactor in place
- If staying on Twilio (no telephony migration) → refactor might be viable

None of these are true.

---

## D.3 — Execution Plan

### Step 1: Freeze old code

```bash
git tag v5-final-freeze
# Old repo preserved at this tag forever
# New development in new repo (or same repo, fresh branch from empty)
```

### Step 2: New repo structure (monorepo)

```
timkame/
├── apps/
│   ├── web/          # Next.js (admin + landing)
│   └── server/       # Backend API (Hono/Fastify)
├── packages/
│   ├── shared/       # Types, constants, utils
│   └── db/           # Drizzle schema + migrations
├── content/
│   └── data/         # Content blocks (pricing.ts, features.ts, etc.)
├── legal/            # ToS, Privacy, AUP (EN + RU)
├── docs/             # All planning docs (carried from old repo)
├── .github/
│   └── workflows/    # CI/CD
├── turbo.json        # Turborepo config
└── package.json      # Root workspace
```

### Step 3: Week-by-week plan (20 hrs/week)

**Week 1-2: Scaffolding**
- Monorepo setup (Turborepo)
- TypeScript strict mode
- ESLint + Prettier + 400-line-max linter rule
- Supabase project + initial schema (organizations, users, phone_numbers)
- Auth (Supabase Auth)
- CI pipeline (GitHub Actions: typecheck + lint + test)
- Doppler secrets setup

**Week 3-4: Telephony + Voice**
- Telnyx account + first number
- Call handling (depends on A.4 gate: direct SIP or middleware)
- Basic call transcript storage
- Smart transfer logic

**Week 5-6: Core Features**
- Prompt editor with PHI scanner
- Telegram notification integration
- Call forwarding wizard (4 carrier tracks)
- Number purchase flow

**Week 7-8: Billing + Dashboard**
- Stripe integration (test mode)
- Usage metering (billable minutes)
- Admin dashboard skeleton (port UI designs)
- Call history + transcript view

**Week 9-10: Safeguards + Polish**
- 8 safeguards (rate limits, content moderation, PHI scan, spend caps, etc.)
- 10DLC automation
- Error handling + Sentry
- End-to-end testing of critical paths

**Phase 2 (Week 11-14): Polish**
- Onboarding analytics
- Prompt templates (5 verticals × 2 languages)
- Help center / FAQ (EN + RU)
- SEO basics + hreflang
- Legal docs live on site
- Landing pages (EN default + /ru separately authored)

### What to port from old codebase:

| Preserve | How |
|----------|-----|
| Admin panel UI designs | Screenshot → rebuild with same Tailwind classes, new component structure |
| Proven voice prompts | Copy prompt text to content blocks |
| Marketing site design | Screenshot → rebuild in new Next.js |
| Content (features, FAQ, testimonials) | Migrate to `/content/data/` TypeScript files |
| n8n workflow logic | Read each workflow → rewrite as TypeScript functions |
| Domain knowledge | Carry docs forward, reference during rebuild |

### What NOT to port:

| Discard | Why |
|---------|-----|
| server.js | 10 mixed concerns, Twilio-specific |
| All API routes | No validation, wrong patterns |
| Database schema | Rebuild with RLS-first, proper FKs |
| n8n as engine | Replace with code-based orchestration |
| Deploy scripts | Rebuild for new infra |
| .env management | Replace with secrets manager |

### Prevent past mistakes:

From commit 1, enforced via CI:
- 400 line/file max (ESLint rule → build fails)
- Zod validation on every API endpoint
- No `any` types (TypeScript strict + ESLint no-explicit-any)
- RLS policies on every table
- Webhook signature verification on every hook
- No PII in logs (custom lint rule)
- Conventional commits (commitlint)
- Test coverage minimum (60%)
