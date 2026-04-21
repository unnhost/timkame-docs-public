# CLAUDE.md — TimkaMe v6

> This file is read automatically at the start of every Claude Code session.
> It is the single source of truth for how to work in this repo.

## Project

**TimkaMe.com** — AI Voice Receptionist for small businesses (US market, Russian-speaking SMBs in NY/NJ).

**Version:** v6 (complete rewrite from v3)
**Phase:** 1 (Core Build) — Week 1-2: Scaffolding + DB + Auth
**Owner:** Eugene (solo founder) + Claude Code (autonomous dev)

---

## Communication Style

- **Default language: Russian.** Eugene writes in Russian, respond in Russian.
- **English only for:** code, file paths, API names, technical identifiers, commit messages, PR titles/bodies.
- Be concise. No fluff, no apologies, no preamble.
- Architecture proposals: 1-sentence recommendation + 1-sentence rationale. Full analysis only if asked.
- Progress reports: what was done + what's next, 5 bullets max.

---

## Work Rules (Hard Constraints)

### Git workflow
- NEVER push directly to `main` — always `branch` -> `PR` -> `CI green` -> `squash merge`
- Branch naming: `feat/...`, `fix/...`, `chore/...`, `docs/...`
- Every commit follows **Conventional Commits** format
- Every AI-assisted commit includes prompt summary in the commit message body

### Code limits (enforced by linter)
- **400 lines/file** max
- **50 lines/function** max
- **3 levels nesting** max
- No global `utils/`, `helpers/`, `services/`, `controllers/`, `models/` folders
- Domain-driven folder structure (see `docs/coding-standards.md` E.2)

### Security
- NEVER commit secrets — scan for `sk_test_`, `eyJ`, `KEY01`, `sbp_`, `supabase` patterns before commit
- Secrets come from Doppler: `doppler secrets get NAME --plain` or `doppler run -- command`
- NEVER print secrets in logs, stdout, or commit messages
- API endpoints ALWAYS require auth check
- RLS is the entire multi-tenant security story

### Testing
- Code compiles: `turbo typecheck`
- Lint clean: `turbo lint`
- Tests pass: `turbo test`
- Build succeeds: `turbo build`

---

## Decision-Making Autonomy

- Make decisions independently within current scope
- Log every non-trivial decision to `docs/DECISIONS.md` (ADR-lite format, see existing entries for template)
- Out-of-scope ideas -> `docs/FUTURE-IDEAS.md` (do NOT build)
- True blockers -> `docs/BLOCKER.md` with clear options, then stop
- Ambiguous user intent -> apply sensible default, document in `DECISIONS.md`

---

## Skills (Always Active)

Before any task, consult relevant skills:

| Skill | When |
|-------|------|
| `.claude/skills/project-memory/SKILL.md` | Timeline + session state |
| `.claude/skills/decision-logger/SKILL.md` | When making decisions |
| `.claude/skills/code-quality/SKILL.md` | When writing code |
| `.claude/skills/content-standards/SKILL.md` | When writing UI text |
| `.claude/skills/security-defaults/SKILL.md` | When handling data/auth |
| `.claude/skills/completion-discipline/SKILL.md` | When claiming "done" |

---

## Context Restore (Session Start)

1. Read `docs/NEXT-SESSION.md` — **primary**, текущее состояние + задачи + проблемы
2. Run `git log --oneline -5` — recent commits
3. Scan `docs/DECISIONS.md` — only if new DEC entries since last session
4. Read `docs/TIMELINE.md` — only if debugging what happened when
5. Announce: "Resuming from [state]. Next: [task]."

---

## Session Modes

Eugene specifies session mode at start. If not specified, assume **глубокая**.

| Mode | When | Rules |
|------|------|-------|
| **короткая сессия** | iPhone / Chrome Remote Desktop | Quick wins only. No refactors. Single-file PRs. No multi-step tasks. |
| **глубокая сессия** | At Mac | Multi-hour tasks OK. Multi-file refactors OK. Full PR workflow. |

---

## Definition of "Done"

- [ ] Code compiles (`turbo typecheck`)
- [ ] Lint clean (`turbo lint`)
- [ ] Tests pass (`turbo test`)
- [ ] Build succeeds (`turbo build`)
- [ ] No secrets in committed files (grep before commit)
- [ ] PR created with descriptive title + body
- [ ] CI green on PR
- [ ] `docs/NEXT-SESSION.md` updated with handoff notes

---

## What Eugene Wants vs. Doesn't Want

### WANTS
- Quality code, production-grade
- Boring tech, proven patterns
- Russian-first UI for end users
- Self-serve onboarding <10 min
- <$100/mo infrastructure (at 0 customers)

### DOES NOT WANT
- Speculative abstractions ("might need later")
- K8s, Kafka, microservices, event sourcing
- Defensive code for impossible edge cases
- 30k-line god files
- Features outside current phase scope
- Essays, summaries, apologies

---

## Architecture (v6)

```
Telnyx SIP -> OpenAI Realtime (direct, no middleware)
                   |
            Hono backend (Hetzner US)
                   |
         Supabase (PostgreSQL + Auth + RLS)
                   |
            Inngest (workflow orchestration)
                   |
      Next.js 15 (App Router, admin dashboard)
```

### Stack (locked decisions)

| Layer | Choice | Decision |
|-------|--------|----------|
| Telephony | Telnyx PAYG | DEC-001, DEC-006 |
| Voice AI | OpenAI Realtime via SIP | DEC-007, DEC-010 |
| Backend | TypeScript + Hono | - |
| Frontend | Next.js 15 App Router | - |
| Database | Supabase (free tier) | - |
| Auth | Supabase Auth | - |
| Workflows | Inngest | - |
| Secrets | Doppler | - |
| CI/CD | GitHub Actions | - |
| Hosting | Hetzner US (Ashburn) | - |

### Monorepo Structure

```
apps/
  server/     — Hono backend (port 5050)
  web/        — Next.js 15 frontend
packages/
  db/         — Drizzle ORM schema + Supabase client
  shared/     — Pricing constants, shared types
```

Build orchestration: Turborepo (`turbo.json`)

---

## Database

**Provider:** Supabase (PostgreSQL with RLS)
**ORM:** Drizzle ORM (schema in `packages/db/src/schema/`)
**Migrations:** `packages/db/supabase/migrations/` (Supabase CLI format)

### Key Tables (v6)
- `organizations` — tenant root, subscription, billing
- `user_profiles` — extends auth.users, FK to org
- `phone_numbers` — Telnyx number mappings
- `calls` — call records with transcripts
- `prompts` — versioned greeting + instructions
- `notifications` — delivery queue (Telegram/email)
- `subscriptions` — billing state
- `audit_log` — compliance tracking
- `rate_limits` — safeguards

### RLS Pattern
```sql
-- Every table uses org_id for tenant isolation
CREATE POLICY "tenant_isolation" ON table_name
  USING (org_id = public.user_org_id());
```

`user_org_id()` — SECURITY DEFINER function in `public` schema (DEC-011).

### Connection
- Session-mode pooler at `aws-1-us-west-2.pooler.supabase.com:5432` (DEC-012)
- Use `supabase db push --db-url` for migrations

---

## Pricing (Single Source of Truth)

Defined in `packages/shared/src/pricing.ts`:
- **$79/mo**, 500 minutes included
- **$0.15/min** overage
- **14-day free trial**, no credit card required
- Single tier only (DEC-002)

---

## Secrets (Doppler)

Available secrets (NEVER print/commit these):
```
SUPABASE_URL, SUPABASE_ANON_KEY, SUPABASE_SERVICE_ROLE_KEY
SUPABASE_PROJECT_REF, SUPABASE_DB_PASSWORD, SUPABASE_ACCESS_TOKEN
TELNYX_API_KEY, TELNYX_PHONE_NUMBER
OPENAI_API_KEY
STRIPE_SECRET_KEY, STRIPE_PUBLISHABLE_KEY
TELEGRAM_BOT_TOKEN
```

Access: `doppler secrets get NAME --plain` or `doppler run -- command`

---

## Quick Commands

```bash
# Development
turbo dev                # Start all apps
turbo typecheck          # TypeScript check
turbo lint               # ESLint
turbo test               # Vitest
turbo build              # Production build

# Supabase
supabase db push --db-url "$(doppler run -- printenv DATABASE_URL)"
supabase migration new description_here

# Doppler
doppler secrets get NAME --plain
doppler run -- npm run dev

# Git (branch workflow)
git checkout -b feat/feature-name
gh pr create --title "feat: description" --body "..."
gh pr checks --watch
gh pr merge --squash --delete-branch
```

---

## Documentation Index

| Doc | Purpose |
|-----|---------|
| `docs/DECISIONS.md` | All architectural decisions (ADR-lite) |
| `docs/TIMELINE.md` | What's been done, with timestamps |
| `docs/NEXT-SESSION.md` | Handoff notes for next session |
| `docs/MASTER-CHECKLIST.md` | Phase 1-2 progress tracking |
| `docs/FUTURE-IDEAS.md` | Out-of-scope ideas (do NOT build) |
| `docs/BLOCKER.md` | Current blockers with options |
| `docs/target-architecture.md` | Full architecture spec |
| `docs/coding-standards.md` | Code rules (400-line, domain folders) |
| `docs/roadmap.md` | 16-20 week implementation plan |
| `docs/phase-0-checklist.md` | Eugene's infra setup tasks |
| `.github/workflows/sync-docs-to-public.yml` | Syncs docs to public mirror for Claude web context |

---

## Red Flags (STOP if you see these)

1. Pushing directly to `main`
2. `git add .env` or committing secrets
3. File exceeding 400 lines
4. Adding features outside current phase scope
5. API endpoint without auth check
6. Mass refactoring without request
7. Global `utils/` or `helpers/` folders
8. Adding dependencies not in approved stack
9. Skipping tests or typecheck

---

## Legal (Quick Reference)

- **No HIPAA** — message-taker only, no PHI (DEC-003)
- **TCPA compliance** — consent required for outbound calls/SMS
- **Recording disclosure** — mandatory before AI interaction
- **Calling hours** — 8 AM to 9 PM recipient's local time
- Full details: `docs/target-architecture.md` section C.7

---

**Last Updated:** 2026-04-19
**CLAUDE.md Version:** 6.0
