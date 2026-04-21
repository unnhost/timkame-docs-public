# Phone Reputation Playbook — TimkaMe v6

> Phase C.6 deliverable. Without this, 30% of outbound calls flagged "Spam Likely" within 2 weeks.
> Compiled 04/18/2026.

---

## Why This Matters

Customers forward their business number to our Telnyx number. If our number gets flagged as spam:
- Their callers see "Spam Likely" or "Scam Likely" on caller ID
- Trust destroyed instantly — customer churns
- Unflagging takes 2-4 weeks per carrier
- Reputation damage compounds across all our numbers

**Prevention is 10x cheaper than remediation.**

---

## 1. STIR/SHAKEN — Level A Attestation

**What:** Cryptographic caller ID authentication. Proves the call originated from a legitimate source.

**Level A (Full Attestation):** "We know who's calling and they have the right to use this number."

**Implementation:**
- Telnyx provides STIR/SHAKEN automatically on all outbound calls
- Verify Level A attestation is enabled (not Level B or C)
- Check in Telnyx dashboard: Messaging → SHAKEN Status

**Verification:** After first outbound call, check SIP headers for `verstat=TN-Validation-Passed-A`.

---

## 2. Branded Calling (CNAM / Verified Calls)

**What:** Business name appears on recipient's phone instead of just a number.

**Implementation:**
- Register business name via Telnyx CNAM (Caller Name) registration
- Submit for each Telnyx number we provision
- Name appears as: "TimkaMe - [Business Name]" or just "[Business Name]"

**Process:**
1. When provisioning new number for customer → register CNAM with customer's business name
2. Telnyx → submits to carrier CNAM databases
3. Takes 24-48 hours to propagate
4. Verify: call from the number to a mobile, check what name appears

**Cost:** Included in Telnyx service (no extra charge).

---

## 3. Warm-Up Schedule

**New numbers are suspicious.** A brand new number making 50 calls on day 1 gets flagged. Warm up gradually.

| Week | Max outbound calls/day | Max SMS/day | Activity |
|------|----------------------|-------------|----------|
| 1 | 10 | 5 | Test calls only, low volume |
| 2 | 25 | 15 | Light real usage |
| 3 | 50 | 25 | Normal ramp |
| 4+ | Normal (per rate limits) | Normal | Full operation |

**Enforcement:** Rate limit table in database per number, with `provisioned_at` date. Auto-apply warm-up schedule for first 28 days.

**Critical:** This applies to outbound only. Inbound calls don't trigger spam flags.

---

## 4. Reputation Monitoring

### Free monitoring
| Service | What it checks | Frequency |
|---------|---------------|-----------|
| **Free Caller Registry** (freecallerregistry.com) | Registers numbers as legitimate | Once per number |
| **Telnyx SHAKEN Status** | STIR/SHAKEN attestation level | Dashboard check weekly |
| **Manual test calls** | What caller ID shows on mobile | Monthly per number |

### Paid monitoring (future, when >20 numbers)
| Service | Cost | What it does |
|---------|------|-------------|
| Hiya | ~$200/mo | Real-time spam score monitoring |
| YouMail | ~$100/mo | Spam database presence check |
| Nomorobo | Free (registration) | Register as legitimate caller |

### Monthly manual check (first 10 numbers)
1. Call from each TimkaMe number to personal cell
2. Check: does "Spam Likely" appear?
3. Check: does business name appear (CNAM)?
4. If flagged → trigger remediation runbook (section 6)

---

## 5. Inbound Behavior

**Callers calling our numbers should NOT see "may be spam."**

**Prevention:**
- Numbers purchased new from Telnyx (not recycled — verify)
- CNAM registered immediately upon purchase
- Inbound-only numbers don't get outbound spam flags
- If customer forwards from their existing number → their number reputation is their responsibility (not ours)

**If customer reports their forwarded number shows spam:**
- Not our problem technically (it's their carrier's number)
- But help them: "Call your carrier and ask them to verify your number / remove spam flag"

---

## 6. Flag Remediation Runbook

**If a TimkaMe number is flagged as spam:**

### Immediate (Day 0)
1. Stop all outbound calls from that number
2. Check: is the flag on all carriers or just one? (Test on Verizon, AT&T, T-Mobile phones)
3. Notify affected customer: "We're working on fixing this"

### Carrier unflag requests (Day 1-3)
| Carrier | Unflag process |
|---------|---------------|
| **T-Mobile** | Submit at callprotection.t-mobile.com |
| **AT&T** | Submit at reportarobocall.com |
| **Verizon** | Email: reportfraud@verizon.com |
| **Hiya** | Submit at hiya.com/registration |
| **Nomorobo** | Submit at nomorobo.com/lookup |
| **YouMail** | Submit at youmail.com |
| **First Orion** | Submit at firstorion.com |

### While waiting (Day 1-14)
- Temporary replacement number if customer's service is impacted
- Continue monitoring unflag requests
- Audit: what caused the flag? Too many outbound calls? Wrong SHAKEN level?

### If unflagging fails (Day 14+)
- Last resort: replace the number entirely
- New number + CNAM registration + warm-up schedule
- Customer updates their forwarding destination
- Document in `docs/incidents/YYYY-MM-DD-number-flagged.md`

---

## 7. Per-Customer Safeguards

| Safeguard | Limit | Why |
|-----------|-------|-----|
| Outbound calls/day | 20 (Starter) | Prevents spam-like behavior |
| Outbound SMS/day | 20 | 10DLC compliance |
| Outbound only as reply to inbound | 24h window | TCPA safe harbor |
| Warm-up schedule | Enforced 28 days | Prevents new-number flagging |
| STIR/SHAKEN | Level A required | Proves legitimacy |
| CNAM | Registered before first call | Business name on caller ID |

---

## Implementation Checklist

### Per number provisioned:
- [ ] Number purchased from Telnyx (verify not recycled)
- [ ] CNAM registered with customer's business name
- [ ] STIR/SHAKEN Level A verified
- [ ] Registered at Free Caller Registry
- [ ] Warm-up schedule applied (28 days)
- [ ] Rate limits configured
- [ ] First manual test call — check caller ID

### Monthly:
- [ ] Manual test call from each active number
- [ ] Check Telnyx SHAKEN status
- [ ] Review any customer complaints about caller ID
