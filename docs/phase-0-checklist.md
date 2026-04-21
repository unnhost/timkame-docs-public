# Phase 0 — Technical Infrastructure Checklist

> Parallel with Phases A–E research. Minimum to start coding.
> **No LLC dependency.** Eugene operates as sole proprietor during development.
> Compiled 04/18/2026.

---

## Checklist

### Identity & Tax
- [ ] **EIN obtained (sole proprietor)** — IRS online, free, 10 minutes
  - Used for: Stripe, Telnyx, bank (if needed pre-LLC)
  - URL: irs.gov → Apply for EIN online

### Code & CI
- [ ] **GitHub repo created** — new repo for v6 rewrite
  - Monorepo structure per rebuild-strategy.md
  - Branch protection on `main`: require PR + CI pass
  - `.gitignore` includes `.env*`, `node_modules/`, `dist/`
- [ ] **GitHub Actions CI pipeline** — runs on every PR
  - `npm run typecheck` → zero errors
  - `npm run lint` → zero warnings (includes 400-line-max rule)
  - `npm test` → all pass
  - `npm run build` → succeeds
- [ ] **Conventional commits** — commitlint configured

### Domain & DNS
- [ ] **timkame.com DNS** — Cloudflare (already exists)
  - Verify A record points to hosting
  - Verify SSL certificate valid
- [ ] **DMARC/SPF/DKIM configured** (H.11)
  - SPF TXT record for Resend
  - DKIM via Resend setup
  - DMARC: start at `p=none`, escalate per schedule
  - Test: send email, check headers for PASS

### Secrets
- [ ] **Doppler (or Infisical) account created** — free solo tier
  - Environments: `development`, `staging`, `production`
  - All secrets stored here, NOT in `.env` files
  - Integration with GitHub Actions for CI
  - Integration with hosting for production

### Telephony
- [ ] **Telnyx account created** — under Eugene's personal account
  - Pay-as-you-go plan (no managed accounts yet — see DEC-006)
  - First phone number purchased (NY area code, $1/mo)
  - API key generated and stored in Doppler
  - Webhook URL configured
- [ ] **10DLC preparation** — understand Telnyx ISV requirements
  - Brand registration docs ready
  - Campaign use case drafted

### Payments
- [ ] **Stripe account** — under Eugene's personal account
  - **TEST MODE ONLY** — no live payments until Phase 0.5 (LLC)
  - Product created: "TimkaMe AI Receptionist"
  - Price created: $79/mo recurring
  - Webhook endpoint configured
  - Restricted API keys (only needed permissions)
  - API keys stored in Doppler

### Observability
- [ ] **Sentry account** — free tier (5K errors/mo, 10K transactions)
  - Project created for backend + frontend
  - DSN stored in Doppler
- [ ] **Upptime status page** — GitHub Actions-based, free
  - Repository created: `timkame/status`
  - Configured to check: backend health, admin panel, voice endpoint
  - Published at: `status.timkame.com`

---

## Verification

All items checked → ready to begin Phase 1 coding.

| Item | How to verify |
|------|--------------|
| EIN | IRS confirmation letter (PDF) |
| GitHub repo | `git clone` succeeds, CI runs on test PR |
| DNS | `dig timkame.com` returns correct IP |
| DMARC/SPF/DKIM | Send test email, check headers |
| Doppler | `doppler run -- echo $TELNYX_API_KEY` returns value |
| Telnyx | API call succeeds, number visible in portal |
| Stripe | Test payment succeeds in test mode |
| Sentry | Test error captured in dashboard |
| Status page | `status.timkame.com` loads, shows green |

---

## NOT in Phase 0

These are Phase 0.5 (pre-launch sequence, triggered when demo-ready + 3 warm prospects):
- LLC filing
- LLC publication
- Registered agent
- Business bank account
- Stripe live mode
- Insurance (GL + cyber)
- Sales tax permit
- Attorney consultation
- Legal docs publication

See `docs/phase-0.5-prelaunch-checklist.md`.
