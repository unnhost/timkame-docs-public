# Phase 0.5 — Pre-Launch Sequence Checklist

> TRIGGERED when ALL conditions met:
> 1. All 10 QA scenarios pass
> 2. At least 3 warm prospects verbally committed to trial
> 3. Claude Code signs off product is production-worthy
> 4. Eugene committed to running as real business
>
> LLC publication = 6-week critical path. Start this early enough.
> Compiled 04/18/2026.

---

## Legal Entity (sequential — LLC publication is critical path)

### Week 1
- [ ] **LLC filed in Albany County** — NY DOS online, ~$200
  - Name: TimkaMe LLC (verify availability first)
  - Albany County chosen for cheap publication ($80 vs $1000+ NYC)
  - Filing: online at dos.ny.gov
- [ ] **Registered agent secured** — Albany County, $100-150/yr
  - Must have physical address in Albany County
  - Options: Northwest Registered Agent, LegalZoom, Incfile

### Week 1-6 (critical path)
- [ ] **LLC publication started** — 6-week clock begins
  - Publish in 2 Albany County newspapers for 6 consecutive weeks
  - Cost: ~$40-80 total (Albany is cheapest in NY)
  - Registered agent often handles this

### Week 2
- [ ] **EIN obtained under LLC** — IRS online, free, replaces sole proprietor EIN
- [ ] **Business bank account opened**
  - Mercury (online, no minimum) or Chase Business (branch required)
  - Deposit initial funds for first month's costs

### Week 7 (after publication completes)
- [ ] **Certificate of Publication filed** — $50, to NY DOS
  - Affidavits from both newspapers
  - Filed with Department of State

---

## Financial (after LLC)

- [ ] **Stripe migrated to LLC**
  - Update business information to LLC
  - Enable LIVE mode
  - Verify bank account connection
  - Test real $1 charge and refund
- [ ] **Telnyx master org migrated to LLC**
  - Update billing to LLC bank account

---

## Insurance (before first payment)

- [ ] **General liability insurance** — $1M coverage
  - Hiscox or Next Insurance
  - ~$300-500/year for SaaS
  - Covers: third-party bodily injury, property damage, personal injury
- [ ] **Cyber liability insurance** — separate from GL!
  - Covers: data breach response, notification costs, forensics, legal defense
  - ~$200-500/year
  - Hiscox bundle with GL for discount
  - **Why separate:** General liability does NOT cover data breach. Voice AI storing transcripts = real data breach risk.

---

## Tax

- [ ] **NY sales tax permit** — if SaaS is taxable in NY
  - Check: SaaS sold to NY customers = likely taxable
  - Register at tax.ny.gov
  - Stripe Tax ($50/mo) or TaxJar ($19/mo) for automated calculation
  - OR manual quarterly filing if <10 customers

---

## Legal Documents (drafted during build, attorney-reviewed now)

- [ ] **Attorney consultation** — $500-1500
  - Review: ToS, Privacy Policy, AUP (EN + RU versions)
  - Specifically review: TCPA compliance, recording consent language, liability limitations
  - Find: attorney familiar with SaaS + TCPA (not general business attorney)

- [ ] **Terms of Service** — published on site (EN + RU)
  - TCPA consent warranty
  - Recording disclosure acknowledgment
  - Refund policy (14-day, pro-rated)
  - Billing terms ($79/mo, overage, dunning)
  - AUP reference
  - Age restriction (13+)
  - Limitation of liability
  - Dispute resolution (arbitration vs court)

- [ ] **Privacy Policy** — published on site (EN + RU)
  - Data categories collected
  - How data is used
  - Data retention (90 days recordings, 5 years consent)
  - Data rights (access, delete, correct, opt-out)
  - Third parties (Telnyx, OpenAI, Stripe, Supabase)
  - Breach notification (72h per NY SHIELD)
  - CCPA-specific section
  - "Do Not Sell" link

- [ ] **Acceptable Use Policy** — published on site (EN + RU)
  - Prohibited uses: spam, TCPA violations, SHAFT content, harassment, PHI collection
  - Consequences: warning → suspension → termination
  - Enforcement: automated (PHI scanner, rate limits) + manual review

---

## Final Verification

Before accepting first payment:

- [ ] LLC Certificate of Publication filed and acknowledged by DOS
- [ ] Business bank account active with LLC as owner
- [ ] Stripe live mode enabled under LLC
- [ ] GL insurance certificate in hand
- [ ] Cyber insurance certificate in hand
- [ ] All legal docs published on timkame.com/terms, /privacy, /aup
- [ ] All legal docs in EN + RU
- [ ] Attorney has signed off on legal docs
- [ ] NY sales tax registration (if applicable)

---

## Timeline Estimate

| Week | Action |
|------|--------|
| 1 | File LLC, secure registered agent, start publication |
| 2 | EIN under LLC, open bank account, start Stripe migration |
| 3-6 | Publication running (waiting period) |
| 3 | Insurance quotes, attorney scheduled |
| 4 | Attorney review of legal docs |
| 5 | Legal docs published, insurance active |
| 6 | Publication period ends |
| 7 | Certificate of Publication filed, Stripe live |
| 7 | **READY TO ACCEPT FIRST PAYMENT** |

**Critical path: 7 weeks from decision to accept customers.**

---

## Until Phase 0.5 Completes — HARD RULES

- ❌ No "TimkaMe LLC" branding anywhere
- ❌ No paid customer contracts
- ❌ No live-mode Stripe collecting real payments
- ❌ No claims of LLC status on site
- ✅ All pre-LLC trials operate as sole proprietor with explicit "beta" disclaimer
- ✅ Beta disclaimer on signup: "TimkaMe is currently in beta. Service provided by [Eugene's name], sole proprietor."
