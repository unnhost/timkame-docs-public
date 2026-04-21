# Coding Standards — TimkaMe v6

> Phase E deliverable. Enforced, not guidelines. Compiled 04/18/2026.

---

## E.1 — File Discipline (HARD)

| Rule | Limit | Enforcement |
|------|-------|-------------|
| Lines per file | **400 max** | ESLint rule → CI build fails |
| Lines per function | **50 max** | ESLint rule |
| Nesting depth | **3 levels max** | ESLint rule |
| Public exports per file | **1 class/service** | Code review |

**No exceptions.** If a file hits 380 lines, split before adding more. "But it's all related" is not a reason — find the seam.

**How to split:**
- Extract types to `types.ts` in same folder
- Extract helpers to `utils.ts` in same folder (NOT a global `utils/`)
- Extract sub-components to separate files
- Extract API calls to `api.ts` in same folder

---

## E.2 — Domain-Driven Folders

```
src/
  auth/           # Login, signup, session, permissions
  billing/        # Stripe, usage metering, invoices, dunning
  calls/          # Call handling, transcripts, recording
  onboarding/     # Forwarding wizard, number setup, 10DLC
  prompts/        # Prompt editor, PHI scanner, templates
  safeguards/     # Rate limits, content moderation, spend caps
  telephony/      # Telnyx client, number management, SIP config
  notifications/  # Telegram, email, notification queue
  shared/         # Types, constants, logger, errors (truly shared only)
```

**NOT:**
```
controllers/   ← banned
models/        ← banned
utils/         ← banned (global)
helpers/       ← banned
services/      ← banned (too vague)
```

Each domain folder contains its own types, routes, logic, and tests. Cross-domain imports go through `shared/`.

---

## E.3 — AI Assistant Discipline

### Feature freeze
New code only from approved scope (Phase 1 or Phase 2 items in MASTER-CHECKLIST). Everything else is out of scope.

### Rejection protocol
When AI suggests any of the following → **REJECT**, log to `docs/ai-rejection-log.md`:
- Extra error handling beyond boundary validation
- "Future flexibility" abstractions
- Retry/caching/fallback "just in case"
- New dependencies not in approved stack
- Refactoring adjacent code that wasn't asked about
- Adding types/docs to unchanged files
- "While we're here, let's also..."

### Logging format
```markdown
### [Date] — [What was rejected]
**Suggested by:** [Claude/Cursor/etc]
**Why rejected:** [1 line]
**Scope it tried to expand:** [1 line]
```

### Weekly review
<5 rejections/week in active dev = filter too loose. Tighten scope instructions.
>20 rejections/week = scope instructions too vague. Clarify.

### Commit discipline
Every AI-assisted commit includes a concise description of what was asked for vs what was produced.

---

## E.4 — Comments and Documentation

### File header (required)
```typescript
/**
 * Handles Telegram notification delivery for call summaries.
 * Sends formatted messages with transcript excerpts to business owner's Telegram.
 */
```
1-3 sentences. What this file is responsible for. Not what it contains.

### JSDoc on exports (required)
```typescript
/**
 * Sends a call summary notification to the business owner's Telegram.
 *
 * @param orgId - Organization ID for tenant lookup
 * @param callId - Call record ID for transcript retrieval
 * @returns Telegram message ID on success, null if owner has no Telegram configured
 *
 * @example
 * const msgId = await sendCallNotification('org_123', 'call_456');
 */
export async function sendCallNotification(orgId: string, callId: string): Promise<string | null> {
```

### Inline comments
- **WHY**, never **WHAT**
- Good: `// Telnyx returns duration in seconds but bills in 6-second increments`
- Bad: `// increment counter by 1`

### Banned
- `// TODO: fix later` — fix now or create a TECHNICAL-DEBT.md entry
- Commented-out code — delete it, git has history
- `// eslint-disable` without justification comment

---

## E.5 — Testing Standards

### What must be tested (critical paths)
1. **Call flow:** inbound → voice AI → transcript → notification
2. **Signup:** register → verify → create org → provision number
3. **Billing:** usage tracking → invoice → overage calculation → dunning
4. **AUP enforcement:** PHI detection → rejection, rate limit → block
5. **Smart transfer:** available → ring → timeout → message-take

### Testing hierarchy
**Integration > unit when they conflict.** A test that hits Supabase and verifies RLS is worth 10 mock-based unit tests.

### Coverage target
**60%.** Not 90%. Diminishing returns past 70% for a solo founder. Focus coverage on critical paths above.

### CI pipeline
```yaml
# Every PR:
- npm run typecheck     # Zero errors
- npm run lint          # Zero warnings
- npm test              # All pass
- npm run build         # Succeeds
```

### Test file location
Co-located: `src/billing/usage.ts` → `src/billing/usage.test.ts`. Not in a separate `__tests__/` folder.

---

## E.6 — Security Defaults

### Every API endpoint MUST have:
1. **Rate limiting** — per-IP and per-org
2. **Input validation** — Zod schema, reject unknown keys
3. **Authentication** — Supabase JWT verified
4. **Authorization** — RLS or explicit org_id check
5. **CORS** — locked to known origins (not `*`)

### Database:
- All queries parameterized (Drizzle ORM, no raw SQL concat)
- RLS on every table, scoped to `org_id`
- Cross-tenant isolation verified in tests

### Webhooks (Telnyx, Stripe):
- Signature verified using provider SDK
- Replay protection (check timestamp, reject >5 min old)
- Idempotency key for state-changing operations

### Dependencies:
- Audited before adding (`npm audit`)
- Maintained (last publish <12 months)
- No `eval()`, `Function()`, or dynamic imports of user strings

### Secrets:
- From Doppler/Infisical only, never `.env` in production
- Never logged, never in error messages
- Rotated on schedule (documented in security-roadmap.md)

### PII:
- Never in logs — sanitize before logging
- Phone numbers: log last 4 digits only (`***-***-1234`)
- Names: never logged
- Transcripts: never logged (stored in DB with RLS)

### Call recordings:
- 2-party consent disclosure hard-coded in AI greeting
- Auto-deletion after 90 days (database job)
- Consent records kept 5 years

---

## E.7 — Git Hygiene

### Branching
- `main` = production (protected)
- Feature branches: `feat/call-forwarding-wizard`, `fix/billing-overage-calc`
- Squash merge to main

### Commits
Conventional commits enforced via commitlint:
```
feat: add Telegram notification for missed calls
fix: correct overage billing for partial minutes
docs: add phone reputation playbook
chore: update Telnyx SDK to 2.x
test: add integration test for RLS cross-tenant isolation
```

### Never
- Direct push to main
- `--no-verify` on commits
- Force push to shared branches
- Commits without running typecheck + lint
