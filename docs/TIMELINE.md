# Timeline — TimkaMe v6

> Старше 14 дней → [TIMELINE-archive.md](TIMELINE-archive.md)

## DONE
- 04/18/2026 5:30 PM EST | Phase 0.5 | Created MASTER-CHECKLIST.md — Eugene's progress dashboard
  Files: docs/MASTER-CHECKLIST.md
- 04/18/2026 5:35 PM EST | Phase 0.5 | Created 6 skills: project-memory, decision-logger, code-quality, content-standards, security-defaults, completion-discipline
  Files: .claude/skills/*/SKILL.md (6 files)
- 04/18/2026 5:40 PM EST | Phase 0.5 | Initialized tracking docs + living trackers
  Files: docs/TIMELINE.md, docs/NEXT-SESSION.md, docs/DECISIONS.md, docs/ai-rejection-log.md, docs/TECHNICAL-DEBT.md, docs/FUTURE-IDEAS.md, docs/skill-feedback.md
- 04/18/2026 5:45 PM EST | Phase B.1-B.3 | Codebase metrics scan complete
  Findings: 104K LOC, 390 files, 18 files >1000 lines, largest 2539 lines (link-manager), server.js 2393 lines, 22 unvalidated API routes, 170 `any` types, 935 lines commented-out code, PII in logs
- 04/18/2026 5:50 PM EST | Phase B.4 | Honest verdict: FULL REWRITE with frontend preservation
  Rationale: 4 god modules, server.js at 2393 lines violates transport-only rule, no tests, 170 any types, zero existing users = no migration cost
- 04/18/2026 6:00 PM EST | Phase A.1 | Pipecat source code reading complete
  Findings: 671-line largest file, async pipeline, observer metrics, local VAD + cloud STT/TTS/LLM pattern
- 04/18/2026 6:10 PM EST | Phase A.1 | Rosie deep dive complete
  Findings: Bland AI backend, $49/250min, EN/ES only, 1700+ customers, <5min onboarding, call forwarding only
- 04/18/2026 6:10 PM EST | Phase A.2 | 5 competitor quick survey complete
  Findings: Russian = real market gap. Only Retell (dev platform) has functional Russian among turnkey competitors
- 04/18/2026 6:15 PM EST | Phase A.3 | LiveKit Agents pattern extracted
  Pattern: state machine per-speech-turn with SpeechHandle priority queue, disallow_interruptions() for tool calls
- 04/18/2026 6:20 PM EST | Telnyx | Managed Accounts research complete
  CRITICAL: $1,000/mo minimum commit. Breaks $100/mo budget. See DEC-006.
  BONUS: Telnyx SIP → OpenAI Realtime direct (150ms, no middleware). See DEC-007.
- 04/18/2026 6:30 PM EST | Phase A.1 | Synthflow deep dive complete
  Findings: $29.8M funded, 72 employees, PAYG $0.11-0.16/min, Russian "listed" but unverified quality
- 04/18/2026 6:30 PM EST | Phase A.4 | Voice provider research complete (web searches)
  Providers: OpenAI Realtime (RU supported, no quality data), ElevenLabs (RU TTS good), Deepgram Nova-3 (RU STT), Gemini 3.1 Flash Live (90+ langs), Ultravox (26 langs incl RU)

- 04/18/2026 9:00 PM EST | Session 2 | Resumed from session 1 with improved v6 prompt
  Updates: EN landing default (was RU), LLC deferred to pre-launch, expanded checklist
- 04/18/2026 9:15 PM EST | Phase A.5 | Competitive moat analysis complete
  10 moats documented. Top 3: native Russian voice quality, Russian name transcription, EN↔RU mid-call switching
- 04/18/2026 9:15 PM EST | Phase A.6 | Synthesis complete
  5 patterns right (call forwarding, message-taker, single tier, pipeline arch, Russian gap), 3 anti-patterns (god file, single vendor, unverified language claims)
- 04/18/2026 9:30 PM EST | Phase A+B | Compiled docs/research-synthesis.md and docs/codebase-audit.md (v6 format)
- 04/18/2026 9:45 PM EST | Phase C | target-architecture.md complete
  Stack: Telnyx PAYG + OpenAI Realtime + Hono + Next.js + Supabase + Inngest. $14/mo @0 customers, $135/mo @10. Hetzner US (Ashburn) $12.77/mo.
- 04/18/2026 9:45 PM EST | Phase C.6 | phone-reputation-playbook.md complete
- 04/18/2026 9:45 PM EST | Phase C.7 | observability-design.md complete (~$5/mo)
- 04/18/2026 10:00 PM EST | Phase D | rebuild-strategy.md complete — FULL REWRITE defended
- 04/18/2026 10:00 PM EST | Phase E | coding-standards.md complete — 400-line cap, domain folders, AI discipline
- 04/18/2026 10:15 PM EST | Phase F | All 5 deliverables complete
  roadmap.md (250-300 hrs total, 16-20 weeks), pre-commitments.md, first-customer-runbook.md, qa-scenarios.md (10 scenarios), unit-economics.md (break-even at 3 customers)
- 04/18/2026 10:15 PM EST | Phase G | security-roadmap.md complete — Day 1 / Month 3 / Month 9+
- 04/18/2026 10:30 PM EST | Phase H | All 12 items complete
  go-no-go-voice-quality.md, billing-lifecycle.md, support-operations.md, product-analytics.md, referral-program.md, disaster-recovery.md
- 04/18/2026 10:30 PM EST | Legal | legal-compliance-map.md (10 US laws), phase-0-checklist.md, phase-0.5-prelaunch-checklist.md
- 04/18/2026 10:30 PM EST | Decisions | DEC-008 (EN landing default), DEC-009 (LLC deferred)

- 04/18/2026 11:00 PM EST | A.4 Voice Gate | ✅ PASSED — OpenAI Realtime Russian quality confirmed by Eugene
  Stack locked: direct Telnyx SIP → OpenAI Realtime. No Pipecat. DEC-010 added.
- 04/18/2026 11:00 PM EST | DEC-006 | RESOLVED — Telnyx PAYG approved. No managed accounts.
- 04/18/2026 11:00 PM EST | Docs | Russian summaries added to 7 key documents. STACK-REVIEW.md written (на русском).
- 04/18/2026 11:00 PM EST | Decisions | DEC-010 (voice stack locked), DEC-006 resolved, DEC-007 resolved

- 04/18/2026 11:30 PM EST | Stack | ✅ APPROVED — Eugene approved all 3 decisions (rewrite, Inngest, Hetzner)
- 04/18/2026 11:30 PM EST | Corrections | 4 pre-approve правки: Coolify CVE verified, Telnyx rate unified ($0.004), latency explained, n8n migration re-estimated
- 04/18/2026 11:45 PM EST | Phase 0 | Guides written — 5 step-by-step guides in docs/phase-0-guides/

- 04/19/2026 9:30 AM EST | Phase 1 | CLAUDE.md rewritten for v6 (PR #3 merged)
  Files: CLAUDE.md
- 04/19/2026 10:00 AM EST | Phase 1 | 9 DB tables created + applied to Supabase
  Tables: organizations, user_profiles, phone_numbers, calls, prompts, notifications, rate_limits, audit_log, subscriptions
  Files: packages/db/supabase/migrations/ (6 files), packages/db/src/schema/ (9 files)
- 04/19/2026 10:30 AM EST | Phase 1 | RLS policies (14) + user_org_id() + signup trigger applied
  Files: migrations 4-6, DEC-011/012/014
- 04/19/2026 10:50 AM EST | Phase 1 | Supabase Auth integrated (server + web)
  Routes: POST signup/login/logout, GET /me, PATCH /users/me/language
  Pages: /auth/login, /auth/signup, /auth/verify, /auth/callback
  Files: apps/server/src/auth/, apps/web/src/app/auth/, middleware.ts
- 04/19/2026 10:55 AM EST | Phase 1 | Cross-tenant isolation test — 14/14 passing
  Files: apps/server/tests/integration/multi-tenant-isolation.test.ts
- 04/19/2026 11:00 AM EST | Phase 1 | Decisions DEC-013/014/015 logged
- 04/19/2026 11:05 AM EST | Phase 1 | Open redirect fix in OAuth callback
- 04/19/2026 11:10 AM EST | Phase 1 | PR #4 merged (DB + RLS + Auth + tests)

- 04/19/2026 2:00 PM EST | Phase 1 | Deploy infra: PM2 ecosystem, CD workflow, nginx config
  Files: ecosystem.config.cjs, .github/workflows/deploy.yml, deploy/nginx/v6.timkame.com.conf
- 04/19/2026 2:00 PM EST | Phase 1 | DEC-018: DNS-only + Let's Encrypt for v6.timkame.com (voice webhook reliability)

- 04/19/2026 10:45 PM EST | Phase 1 | v6 deployed to Hetzner alongside v5
  Infra: PM2 (server:5051 tsx, web:3002), nginx + Let's Encrypt, Doppler prd, GitHub Actions CD
  URL: https://v6.timkame.com (health + frontend + API verified)
  Fixes: tsx runtime (ESM resolution), port 3002 (3001 taken by Docker), nginx SSL block, doppler run for build

## IN PROGRESS
- Phase 1 Week 3-4: Telephony + Voice

## TODO
- Telnyx SDK integration (number provisioning, SIP)
- OpenAI Realtime direct SIP configuration
- Inbound call flow + transcript storage
- Inngest workflows for post-call processing
- Legal document drafts (ToS, Privacy, AUP) — during Phase 1 build
- Phase 2 Polish
- Phase 0.5 Pre-Launch — triggered when demo-ready + 3 warm prospects
