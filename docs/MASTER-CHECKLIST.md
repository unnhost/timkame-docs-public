# Master Checklist — TimkaMe v6

Last updated: 04/19/2026 11:15 AM EST
Legend: [ ] not started · [~] in progress · [x] done · [!] blocked

[Phase 0.5: 6/6 done] [Phase A: 9/9 done] [Phase B: 5/5 done] [Phase C: 8/8 done] [Phase D: 3/3 done] [Phase E: 7/7 done] [Phase F: 5/5 done] [Phase G: 3/3 done] [Phase H: 12/12 done] [Legal: 3/4 done]

## Phase 0.5 — Skills (Claude Code)
- [x] project-memory/SKILL.md
- [x] decision-logger/SKILL.md
- [x] code-quality/SKILL.md
- [x] content-standards/SKILL.md (updated: EN default)
- [x] security-defaults/SKILL.md
- [x] completion-discipline/SKILL.md

## Phase A — Research (Claude Code)
- [x] A.1 — Rosie deep dive (Bland AI backend, $49/250min, EN/ES only, 1700+ customers)
- [x] A.1 — Synthflow deep dive ($29.8M funded, PAYG $0.11-0.16/min, Russian unverified)
- [x] A.1 — Pipecat source code (async pipeline, observer metrics, median 162 lines)
- [x] A.2 — 5 competitor survey (Russian = real gap, only Retell has it)
- [x] A.3 — LiveKit Agents: state machine per-speech-turn, interruptibility policy
- [x] A.4 — Russian voice benchmark ✅ PASSED (OpenAI Realtime, confirmed 04/18/2026)
- [x] A.5 — Competitive moat analysis (10 moats documented)
- [x] A.6 — Synthesis (5 right patterns, 3 anti-patterns, stack convergence)
- [x] docs/research-synthesis.md compiled

## Phase B — Codebase Audit (Claude Code)
- [x] B.1 — 30k-line file analysis (18 files >1000 lines)
- [x] B.2 — Metrics (104K LOC, 390 files, 170 any, 22 unvalidated routes)
- [x] B.3 — Architectural smells (4 god modules, PII in logs, no Zod)
- [x] B.4 — Honest verdict: **FULL REWRITE with frontend preservation**
- [x] docs/codebase-audit.md compiled

## Phase C — Target Architecture (Claude Code)
- [x] C.1 — Stack recommendation table with costs ($14/mo @0, $135/mo @10)
- [x] C.2 — System architecture diagrams (4 Mermaid)
- [x] C.3 — Data model + RLS policies (8 tables)
- [x] C.4 — Multi-tenant boundary (RLS-implicit, cross-tenant test)
- [x] C.5 — Content blocks system (Option B: MDX + shared data files)
- [x] C.5 — Language-aware routing (EN default, /ru separate, hreflang)
- [x] C.6 — Phone reputation playbook (docs/phone-reputation-playbook.md)
- [x] C.7 — Observability design (docs/observability-design.md, ~$5/mo)

## Phase D — Rebuild Strategy (Claude Code)
- [x] D.1 — Honest analysis of 3 options
- [x] D.2 — Recommendation: full rewrite (defended with 3 reasons, 2 risks)
- [x] D.3 — Execution plan (monorepo, week-by-week, port vs discard)

## Phase E — Coding Standards (Claude Code)
- [x] E.1 — File discipline (400 lines/file, 50 lines/function, ESLint enforced)
- [x] E.2 — Domain-driven folders (auth/billing/calls/onboarding/...)
- [x] E.3 — AI assistant discipline (rejection log, feature freeze)
- [x] E.4 — Comments and docs (file header, JSDoc, WHY not WHAT)
- [x] E.5 — Testing standards (60% coverage, integration > unit)
- [x] E.6 — Security defaults (Zod, RLS, webhook verify, no PII in logs)
- [x] E.7 — Git hygiene (conventional commits, squash merge, no direct-to-main)

## Phase F — Implementation Roadmap (Claude Code)
- [x] Roadmap with realistic hours (docs/roadmap.md, 250-300 hrs total)
- [x] Pre-commitments doc (docs/pre-commitments.md)
- [x] First-customer runbook (docs/first-customer-runbook.md)
- [x] QA scenarios — 10 (docs/qa-scenarios.md)
- [x] Unit economics model (docs/unit-economics.md)

## Phase G — Security Roadmap (Claude Code)
- [x] Day 1 security requirements (secrets, HTTPS, RLS, webhooks, audit)
- [x] Month 3 plan (OWASP ZAP, Renovate, SOC 2 readiness checklist)
- [x] Month 9+ plan (SOC 2 Type 2 if enterprise, secret rotation)

## Phase H — Operational Completeness (Claude Code)
- [x] H.1 — Voice quality gate (docs/go-no-go-voice-quality.md)
- [x] H.2 — Billing lifecycle (docs/billing-lifecycle.md)
- [x] H.3 — Refund policy (in billing-lifecycle.md)
- [x] H.4 — Support operations (docs/support-operations.md)
- [x] H.5 — Changelog system (timkame.com/changelog, biweekly, EN+RU, in roadmap)
- [x] H.6 — Product analytics (docs/product-analytics.md, PostHog free)
- [x] H.7 — Cyber insurance (in phase-0.5-prelaunch-checklist.md)
- [x] H.8 — Sales tax plan (in phase-0.5-prelaunch-checklist.md)
- [x] H.9 — Referral program (docs/referral-program.md, $20/$20)
- [x] H.10 — Data export/deletion spec (in roadmap Phase 1)
- [x] H.11 — Email auth DMARC/SPF/DKIM (in phase-0-checklist.md)
- [x] H.12 — Backup restore drill (docs/disaster-recovery.md, quarterly)

## Legal Drafts (attorney-reviewed in Phase 0.5)
- [x] legal-compliance-map.md (10 US laws mapped)
- [ ] terms-of-service (EN + RU) — drafted during Phase 1, attorney review in Phase 0.5
- [ ] privacy-policy (EN + RU) — drafted during Phase 1, attorney review in Phase 0.5
- [ ] acceptable-use-policy (EN + RU) — drafted during Phase 1, attorney review in Phase 0.5

## Phase 0 — Technical Infrastructure (Eugene)
- [x] EIN obtained (sole proprietor)
- [x] GitHub repo + Actions CI
- [x] Domain + DNS verified
- [ ] DMARC/SPF/DKIM configured
- [x] Doppler secrets manager
- [x] Master Telnyx org (personal account)
- [x] Stripe test mode (personal account)

## Phase 0.5 — Pre-Launch Sequence (Eugene — TRIGGERED when demo-ready + 3 warm prospects)
- [ ] LLC filed in Albany County
- [ ] LLC publication started (6-week clock)
- [ ] Registered agent secured ($100-150/yr)
- [ ] Business bank account under LLC
- [ ] Stripe migrated to LLC, live mode enabled
- [ ] General liability insurance active
- [ ] Cyber liability insurance active
- [ ] NY sales tax permit (if applicable)
- [ ] Telnyx master org migrated to LLC
- [ ] Certificate of Publication filed ($50)
- [ ] Attorney consultation ($500-1500)
- [ ] Legal docs attorney-reviewed, live on site

## Phase 1 — Core Build (Claude Code + Eugene, 165 hrs / 8-9 weeks)
- [x] Week 1-2: Scaffolding (monorepo, schema, auth, CI, deploy) — PRs #1-4 merged
- [~] Week 3-4: Telephony + Voice (Telnyx SIP done, transcripts done, transfer pending)
- [~] Week 5-6: Core Features (forwarding wizard, **prompts done PR#19**, notifications)
- [ ] Week 7-8: Billing + Dashboard (Stripe, metering, admin UI)
- [ ] Week 9-10: Safeguards + Testing (rate limits, moderation, QA)

## Phase 2 — Polish (Claude Code + Eugene, 62 hrs / 3-4 weeks)
- [ ] Onboarding analytics + prompt templates
- [ ] Landing pages (EN default + /ru separately authored)
- [ ] Help center / FAQ (EN + RU)
- [ ] Founder admin tools
- [ ] SEO + hreflang + legal docs live
- [ ] Status page + synthetic monitoring
- [ ] Referral program

## Pre-Launch Final Check (blocks first payment)
- [ ] All 10 QA scenarios pass
- [ ] Phase 0.5 complete (LLC, insurance, Stripe live, legal docs)
- [ ] Russian voice verified in production
- [ ] EN landing live with prominent RU button
- [ ] RU landing live (separately authored)
- [ ] Support channels tested
- [ ] Status page live
- [ ] Synthetic monitoring 7 days zero false alarms
- [ ] Unit economics profitable at 10 customers

## Post-Launch 30-Day Validation
- [ ] Week 1: 10 warm outreach
- [ ] Week 2: 3 FB group posts
- [ ] Week 3: 10 Brighton walk-ins
- [ ] Week 4: tally + assess vs pre-commitments

## Living Trackers (permanently updated)
- [x] TIMELINE.md current
- [x] DECISIONS.md current
- [x] NEXT-SESSION.md current
- [ ] ai-rejection-log.md updated weekly
- [ ] TECHNICAL-DEBT.md current
- [ ] FUTURE-IDEAS.md current
