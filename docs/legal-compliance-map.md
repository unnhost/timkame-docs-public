# Legal Compliance Map — TimkaMe v6

> 10 US laws mapped to specific TimkaMe obligations. Compiled 04/18/2026.
> **These are draft requirements. Attorney review required in Phase 0.5 before going live.**

---

## 1. TCPA (Telephone Consumer Protection Act)

**What it covers:** Outbound calls, SMS, automated dialers, prerecorded messages.

**TimkaMe exposure:** Smart transfer (outbound leg to owner), any future outbound call features.

**Requirements:**
- Prior express consent before any automated outbound call
- Outbound calls only as reply to inbound within 24h window (our safeguard)
- No auto-dialer to cell phones without written consent
- Calling hours: 8 AM – 9 PM in recipient's local time
- DNC (Do Not Call) list checking before outbound
- STOP/opt-out mechanism for any SMS

**ToS clause:** "Customer warrants compliance with TCPA for all outbound communications initiated through TimkaMe."

**AUP clause:** "Prohibited: using TimkaMe to make unsolicited outbound calls or send spam SMS."

**Penalty:** $500-$1,500 per violation.

---

## 2. Two-Party Consent (State Recording Laws)

**States:** CA, FL, IL, MD, MA, MT, NH, PA, WA (+ others with varying requirements).

**TimkaMe exposure:** Every call is recorded and processed by AI.

**Requirements:**
- Mandatory disclosure in AI greeting: "This call may be recorded and you will be speaking with an AI assistant."
- Disclosure hard-coded — not editable by customer in prompt editor
- Disclosure before any recording begins
- If caller objects → option to transfer to human or end call

**Implementation:** First sentence of every AI greeting is the disclosure. Not configurable. Not removable.

**ToS clause:** "All calls are recorded. Recording disclosure is automatically provided to all callers."

---

## 3. CCPA/CPRA (California Consumer Privacy Act)

**What it covers:** CA residents' data rights: access, deletion, correction, opt-out of sale.

**TimkaMe exposure:** Any CA resident who calls a TimkaMe customer.

**Requirements:**
- Right to know what data is collected (Privacy Policy)
- Right to delete (one-click account deletion + caller data deletion on request)
- Right to correct (update personal info)
- Right to opt-out of data "sale" (we don't sell, but must state this)
- 30-day SLA on data access/deletion requests
- "Do Not Sell My Personal Information" link on website

**Privacy Policy sections:**
- Categories of data collected (names, phone numbers, call recordings, transcripts)
- How data is used (call handling, notifications, billing)
- Data retention (90 days recordings, 5 years consent records)
- How to exercise rights (email or in-app)

---

## 4. State Privacy Collective (VA CDPA, CO CPA, CT CTDPA, TX DPDPA, UT UCPA, OR OCPA)

**What they cover:** Privacy rights similar to CCPA but in 6+ additional states.

**TimkaMe approach:** Treat all US users as having CCPA-level rights. One policy covers all.

**Requirements (collective):**
- Data access/deletion/correction rights
- Opt-out of targeted advertising (not applicable — we don't do targeted ads)
- Data protection assessment for high-risk processing (call recording = high-risk)
- Consent for sensitive data (none collected — no biometrics, health, etc.)

**Privacy Policy:** "We extend the same data rights to all US users regardless of state."

---

## 5. NY SHIELD Act

**What it covers:** Data security requirements for businesses holding NY residents' private data.

**TimkaMe exposure:** All NY customers + their callers.

**Requirements:**
- Reasonable administrative, technical, and physical safeguards
- Employee training on data security (N/A — solo founder, but document practices)
- Risk assessment of data security program
- **72-hour breach notification** to affected individuals
- Notification to NY Attorney General if >500 residents affected

**Implementation:**
- Incident response runbook with 72-hour notification template
- Supabase PITR + encryption at rest
- Secrets management (Doppler)
- RLS + access controls

---

## 6. FTC Section 5 (Unfair or Deceptive Acts)

**What it covers:** False advertising, deceptive business practices.

**TimkaMe exposure:** Marketing claims, pricing, testimonials.

**Requirements:**
- All marketing claims substantiable ("AI answers 95% of calls" → must have data)
- Transparent pricing (no hidden fees — single price, clear overage)
- Real testimonials only (first name + neighborhood + business type)
- "AI" clearly disclosed — never imply it's a human receptionist
- Free trial terms clearly stated (14 days, no CC, what happens after)

**Content standards:** All pricing from content blocks (single source of truth). No unsubstantiable claims.

---

## 7. 10DLC (10-Digit Long Code) Carrier Rules

**What it covers:** SMS sending from standard phone numbers.

**TimkaMe exposure:** Any SMS notifications to callers (confirmation, follow-up).

**Requirements:**
- Brand registration with The Campaign Registry (TCR) via Telnyx
- Campaign use case registration (accurate description)
- STOP keyword → immediate opt-out
- HELP keyword → support info response
- No SHAFT content (Sex, Hate, Alcohol, Firearms, Tobacco)
- Message throughput limits per campaign type

**Implementation:** Telnyx ISV API handles brand + campaign registration. Rate limits in code.

---

## 8. NYC Local Law 144 (AI in Employment Decisions)

**Applicability:** Currently NOT applicable. Applies to AI used in hiring/promotion decisions.

**TimkaMe relevance:** We don't make employment decisions. Our AI handles customer calls.

**Flag for future:** If TimkaMe ever handles job inquiry calls or screens applicants → this becomes mandatory (bias audit, notice requirements). Currently excluded.

---

## 9. CAN-SPAM Act

**What it covers:** Commercial email messages.

**TimkaMe exposure:** Any promotional or marketing emails.

**Requirements:**
- Physical mailing address in every email
- Working unsubscribe mechanism (processed within 10 business days)
- Honest subject lines (no deceptive headers)
- Clear identification as advertisement
- Not applicable to transactional emails (invoices, password resets, service notifications)

**Implementation:** Resend handles unsubscribe. Physical address = registered agent address or PO Box.

---

## 10. COPPA (Children's Online Privacy Protection Act)

**What it covers:** Data collection from children under 13.

**TimkaMe exposure:** Callers could theoretically be children.

**Requirements:**
- Explicit disclaimer: "TimkaMe is not intended for use by individuals under 13"
- No knowingly collected data from children
- If child data discovered → delete immediately

**ToS clause:** "TimkaMe is not intended for use by individuals under 13 years of age."

**Implementation:** No special age-gating needed — service is B2B, sold to business owners.

---

## Compliance Matrix

| Law | ToS | Privacy Policy | AUP | Technical Control | Phase |
|-----|-----|---------------|-----|-------------------|-------|
| TCPA | ✓ Consent warranty | | ✓ No spam calls | Outbound rate limits, 24h window | Phase 1 |
| Two-party consent | ✓ Recording disclosure | ✓ Recording described | | Hard-coded AI greeting | Phase 1 |
| CCPA/CPRA | ✓ Data rights | ✓ Full disclosure | | One-click delete, 30-day SLA | Phase 1 |
| State Privacy | ✓ Universal rights | ✓ All states covered | | Same as CCPA | Phase 1 |
| NY SHIELD | | ✓ Security practices | | 72h breach notification | Phase 0.5 |
| FTC Section 5 | ✓ Honest claims | | | Content blocks, no false claims | Phase 1 |
| 10DLC | | | | Telnyx ISV registration | Phase 1 |
| NYC LL144 | | | | N/A (flagged for future) | — |
| CAN-SPAM | | | | Physical address, unsubscribe | Phase 2 |
| COPPA | ✓ Age disclaimer | ✓ Under-13 exclusion | | No technical control needed | Phase 1 |

---

## Attorney Review Checklist (Phase 0.5)

- [ ] ToS draft reviewed — TCPA warranty, recording disclosure, refund policy, age disclaimer
- [ ] Privacy Policy reviewed — data categories, retention, rights, breach notification
- [ ] AUP reviewed — prohibited uses, TCPA, SHAFT
- [ ] EN + RU versions reviewed (separately authored, legally equivalent)
- [ ] Consent flow reviewed (recording disclosure language specifically)
- [ ] Liability limitations reviewed (state-specific requirements)
- [ ] Indemnification clauses reviewed
- [ ] Arbitration vs court jurisdiction decided
