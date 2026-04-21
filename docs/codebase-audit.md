# Codebase Audit — TimkaMe v6

> Phase B deliverable. Brutal. No flattery. Compiled 04/18/2026.

---

## B.1 — Files >1000 Lines

| File | Lines | Concerns Mixed | AI-Slop % | Verdict |
|------|-------|---------------|-----------|---------|
| `admin-panel/app/admin/link-manager/page.tsx` | 2539 | UI + API calls + state + validation + scanning + updating | ~60% | **Rewrite** — 5+ concerns in one file |
| `server.js` | 2393 | Transport + routing + auth + billing + Twilio + Pipecat + n8n + error handling + logging + config | ~40% | **Rewrite** — 10 mixed concerns, violates transport-only rule |
| `admin-panel/app/admin/quality-dashboard/page.tsx` | ~1500 | UI + analytics + state + charts + filtering | ~50% | **Rewrite** |
| `admin-panel/app/admin/support-analytics/page.tsx` | ~1400 | UI + stats + state + filtering | ~50% | **Rewrite** |
| `admin-panel/app/admin/cloudflare-analytics/page.tsx` | ~1300 | UI + API + state + charts | ~50% | **Rewrite** |
| `admin-panel/app/admin/landing-page-generator/page.tsx` | ~1200 | UI + generation + state + preview | ~50% | **Rewrite** |
| Multiple others >1000 | Varies | Various | ~40-60% | **Rewrite** |

**18 files exceed 1000 lines.** The largest (link-manager at 2539) is evidence of uncontrolled AI-assisted code generation. server.js at 2393 lines with 10 mixed concerns is the canonical example of why this codebase needs a full rewrite.

The "30K line file" from the v6 prompt wasn't found — server.js is 2393 lines. Possible explanations: it was larger before recent refactoring, or 30K referred to total LOC (actual: 104K, which is worse).

---

## B.2 — Metrics

| Metric | Value | Severity |
|--------|-------|----------|
| **Total LOC** | 104,000 | Excessive for feature set |
| **Total files** | 390 | High |
| **Median file size** | ~150 lines | OK |
| **Max file size** | 2,539 lines | SEVERE RED |
| **Files >500 lines** | ~35 | RED |
| **Files >1000 lines** | 18 | SEVERE RED |
| **`any` type usage** | 170 instances | RED — type safety absent |
| **API routes without input validation** | 22 | CRITICAL — no Zod, no schema |
| **Test files** | 0% coverage | CRITICAL |
| **TODO/FIXME count** | Not measured (estimated ~50+) | |
| **Commented-out code blocks** | 935 lines | RED — dead code committed |
| **Dependencies (unused)** | Not audited with depcheck | Likely 10+ unused |

**Honest assessment:** 104K LOC for what is essentially an AI answering service with an admin panel is 3-5x more code than needed. A clean rebuild would likely be 20-30K LOC with better test coverage.

---

## B.3 — Architectural Smells

### Top 10 Problems (ranked by severity × effort)

| # | Problem | Severity | Effort to Fix | Category |
|---|---------|----------|--------------|----------|
| 1 | **server.js: 10 concerns in one file** | CRITICAL | HIGH (rewrite) | God module |
| 2 | **22 API routes with no input validation** | CRITICAL | MEDIUM | Security |
| 3 | **170 `any` types** | HIGH | MEDIUM | Type safety |
| 4 | **0% test coverage** | HIGH | HIGH | Testing |
| 5 | **PII in log output** | CRITICAL | LOW | Security/Legal |
| 6 | **No Zod/schema validation anywhere** | HIGH | MEDIUM | Security |
| 7 | **935 lines commented-out code** | MEDIUM | LOW | Code hygiene |
| 8 | **link-manager: 2539 lines, 5+ concerns** | HIGH | HIGH (rewrite) | God module |
| 9 | **No webhook signature verification** | CRITICAL | LOW | Security |
| 10 | **Unscoped multi-tenant queries** | CRITICAL | HIGH | Security |

### Full Smell Inventory

**God modules (5+ concerns in one file):**
- `server.js` — transport, routing, auth, billing, Twilio, Pipecat, n8n, error handling, logging, config
- `link-manager/page.tsx` — UI, API, state, validation, scanning, updating
- `quality-dashboard/page.tsx` — UI, analytics, state, charts, filtering
- `support-analytics/page.tsx` — UI, stats, state, filtering

**Security:**
- Raw SQL concatenation (not confirmed, but no Zod = likely)
- 22 unvalidated API routes — any request body accepted
- PII (phone numbers, names) appearing in log output
- Missing webhook signature verification on Twilio/Stripe hooks
- Unscoped multi-tenant queries — RLS not enforced at app layer
- CORS likely misconfigured (wildcard `*` found in widget routes)

**Code quality:**
- 170 `any` types — TypeScript effectively disabled
- 935 lines of commented-out code committed
- No tests (0% coverage)
- Pattern inconsistencies across files (mixed styles from different AI sessions)
- Unused dependencies likely present

**Architecture:**
- n8n workflows (37) as critical business logic — not version-controlled, not testable
- Mixing of transport and business logic in server.js
- No domain-driven organization — flat file structure
- No error boundaries or graceful degradation

---

## B.4 — Honest Verdict

### **FULL REWRITE with frontend preservation.**

**Defense:**

1. **server.js alone justifies rewrite.** 2393 lines, 10 mixed concerns, violates the project's own "transport-only" rule. This isn't refactorable — the concerns are too entangled. Strangler fig would take 2x longer than rewrite for same result.

2. **Zero users = zero migration cost.** No customers to migrate, no data to port, no API contracts to honor. This is the one time in a product's life where rewrite is free.

3. **Learning-first priority.** Eugene explicitly chose learning over speed. A rewrite with proper standards from commit 1 teaches more than patching a codebase built by unconstrained AI sessions.

4. **Security baseline too low to patch.** 22 unvalidated routes, PII in logs, no webhook verification, unscoped multi-tenant queries. Patching each is possible but the foundation is wrong — adding validation to 22 routes in a codebase with no validation pattern means each will be done differently.

5. **18 files >1000 lines.** The 400-line cap from coding standards (Phase E) would require splitting every one. At that point you're rewriting anyway.

**What to preserve from old codebase:**
- Admin panel UI designs and component layouts (visual design is good)
- Proven voice prompts that work well
- Marketing site design
- Content (migrated to content blocks system)
- Domain knowledge embedded in n8n workflows (as TypeScript logic)

**What to discard:**
- All backend code (server.js, routes, utils)
- Current database schema (rebuild with RLS-first)
- n8n as workflow engine (replace with code-based orchestration)
- Deployment scripts (rebuild for new infra)
- All .env file management (replace with Doppler or similar)

**Hour estimate at 20 hrs/week:** See Phase F roadmap. ~150-200 hrs for Phase 1 core build = 8-10 weeks.

**Top 2 risks:**
1. **Scope creep during rewrite.** Mitigation: Phase E coding standards + AI rejection log + strict feature freeze.
2. **Voice quality gate fails.** Mitigation: H.1 defines pivot options. Don't start coding voice pipeline until A.4 passes.

**What would change this verdict:** If server.js were <500 lines with clean separation, and API routes had validation, strangler fig would be viable. But they're not.
