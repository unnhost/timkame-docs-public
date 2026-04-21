# Research Synthesis — TimkaMe v6

> Phase A deliverable. Compiled 04/18/2026.

---

## A.1 — Deep Dives

### Rosie (heyrosie.com)

**What they are:** Turnkey AI receptionist for SMBs. ~1700+ customers. EN/ES only.

**Stack:** Bland AI backend (white-label voice platform). Twilio for telephony.

**Pricing:** $49/mo for 250 minutes. Simple single tier.

**Onboarding:** <5 min. Customer keeps existing number, sets up call forwarding. Rosie provisions a forwarding number. No BYOC, no porting required.

**Strengths:**
- Dead simple onboarding — call forwarding only, no technical setup
- $49 price point attracts price-sensitive SMBs
- Good enough English voice quality for message-taking
- Bland AI handles the hard voice infra

**Weaknesses:**
- No Russian. No path to Russian. Bland AI doesn't support it.
- Limited customization — canned prompts, no prompt editor
- Dependent on Bland AI for everything — if Bland changes pricing or goes down, Rosie is dead
- No self-serve billing dashboard — black box usage

**What we steal:** Call forwarding onboarding model. $49–79 price point validation. "It just works" simplicity.

**What we avoid:** Single-vendor voice dependency. No-customization trap.

---

### Synthflow

**What they are:** VC-funded ($29.8M) AI voice platform. 72 employees. PAYG model.

**Pricing:** $0.11–0.16/min pay-as-you-go, or $29–$450/mo for bundled plans.

**Russian claim:** Listed as supported language. Quality unverified — no public Russian demos found. Marketing claims ≠ production quality.

**Strengths:**
- Broad language list (marketing)
- PAYG pricing attractive for testing
- Well-funded team shipping fast
- Extensive docs, API-first

**Weaknesses:**
- Russian quality unverified — "listed" doesn't mean "good"
- Enterprise-focused, not SMB-turnkey
- Complex setup for non-technical users
- PAYG can get expensive at volume ($0.16/min × 500 min = $80/mo just voice)

**What we steal:** Nothing directly — different market segment.

**What we avoid:** Claiming language support without verifying quality. PAYG-only pricing that makes unit economics unpredictable.

---

### Pipecat (open source)

**What it is:** Open-source Python framework for voice AI pipelines. AsyncIO-based.

**Architecture:**
- Pipeline pattern: Source → Processor → Processor → Sink
- Each processor is a small module (median 162 lines)
- Observer pattern for metrics/events
- Local VAD → Cloud STT → LLM → Cloud TTS pipeline

**File discipline:** Median file 162 lines. Largest file 671 lines (main handler — the weakness).

**Strengths:**
- Clean async pipeline architecture
- Small focused modules — good separation of concerns
- Observer pattern for monitoring without coupling
- Active open-source community

**Weaknesses:**
- 671-line main handler violates its own module discipline
- Python — adds complexity if rest of stack is TypeScript
- Deployment overhead for a Python service alongside Node.js
- No built-in multi-tenancy — each instance serves one tenant config

**Patterns to copy:**
- Pipeline as sequence of small async processors
- Observer for metrics (latency, errors, quality)
- VAD (voice activity detection) locally, everything else cloud
- Clear module boundaries: transport / STT / LLM / TTS / state

**Patterns to avoid:**
- God handler file that grows over time
- No state management pattern for multi-turn conversations

---

## A.2 — Competitor Quick Survey

| Competitor | Pricing | Telephony | Russian? | Strength | Weakness | Red Flag |
|-----------|---------|-----------|----------|----------|----------|----------|
| **My AI Front Desk** | $99/mo, 200 min | Twilio | No | Good onboarding wizard | Expensive per-minute ($0.50) | Pricing changed 3x in 12 months |
| **Goodcall** | $59/mo | Own infra | No | Restaurant vertical focus | Narrow vertical | Pivoted twice |
| **Dialzara** | $29/mo + usage | Twilio | No | Cheapest entry | Limited features | Minimal team |
| **Retell AI** | Dev platform, usage-based | Own | **Yes (dev API)** | Best API, multi-language | Not turnkey, requires coding | Dev platform, not end-user product |
| **Bland AI** | $0.09/min | Own | No | Powers Rosie, reliable | No Russian, API-only | Rosie dependency |

**Key finding: Russian = real market gap.** Only Retell has functional Russian among surveyed competitors, and Retell is a developer platform, not a turnkey SMB product. Zero turnkey competitors serve Russian-speaking SMBs.

---

## A.3 — LiveKit Agents Pattern

**Extracted pattern:** State machine per speech turn with interruption policy.

**How it works:**
1. Each speech turn is a `SpeechHandle` with a priority queue
2. When bot is speaking and user interrupts: `interruptibility_policy` decides behavior
3. Tool calls use `disallow_interruptions()` — bot says "one moment" and ignores interruption during API calls
4. After tool completes, bot re-allows interruptions

**Turn detection:** Two-tier system — quick detection (85% accuracy, fast) and verified detection (97% accuracy, slight delay). Prevents false interruptions from background noise.

**What we steal:**
- Interruption policy per-action (message-taking = interruptible, transfer = not interruptible)
- State machine approach to conversation turns
- Two-tier turn detection for noise resilience

---

## A.4 — Voice Provider Research

### Providers evaluated (web research, NOT live audio testing)

| Provider | Russian TTS | Russian STT | Latency | Cost | Notes |
|----------|------------|------------|---------|------|-------|
| **OpenAI Realtime** | Supported (quality unknown) | Supported | 150-200ms (direct SIP) | ~$0.06/min | Direct Telnyx SIP integration. If Russian is good, this eliminates middleware. |
| **ElevenLabs** | Good Russian TTS reported | N/A (TTS only) | ~200-400ms | $0.18/1K chars | Best pure TTS quality, but TTS-only. Need separate STT. |
| **Deepgram Nova-3** | N/A (STT only) | Russian STT supported | ~100-200ms | $0.0059/min | Best STT value. Pair with separate TTS. |
| **Gemini 3.1 Flash Live** | 90+ languages | 90+ languages | ~200-300ms | Usage-based | Google's multimodal. Untested for voice quality. |
| **Ultravox** | 26 languages incl. Russian | 26 languages | ~200ms | Usage-based | Full pipeline. Russian quality unverified. |

### GO/NO-GO Gate Status: BLOCKED

**What's needed:** Eugene must conduct a blind listening test with real Russian names (Людмила Сергеевна, Жорик, Матвей, Светлана, Екатерина). 30-minute session. Score each provider 1-10 on:
- Natural prosody (does it sound like a real Russian speaker?)
- Name pronunciation (especially patronymics and diminutives)
- Foreign accent in TTS (robot accent vs native)
- STT accuracy (does it transcribe "Жорик" correctly or as "Jorik"?)
- EN↔RU code-switching (can it handle mixed-language mid-sentence?)

**Threshold:** ≥1 stack must score 7/10 average. If none pass → STOP, evaluate pivot options (see H.1).

**Architectural implication:**
- If OpenAI Realtime passes → Direct Telnyx SIP, no middleware. Simplest stack. See DEC-007.
- If OpenAI fails but ElevenLabs + Deepgram pass → Hybrid pipeline (Pipecat or custom middleware). More complex but best-of-breed audio.
- If all fail → Wait 3 months, or pivot to English-speaking immigrant SMB beachhead.

---

## A.5 — Competitive Moat Analysis

10 specific moats that Rosie/Synthflow/competitors structurally cannot replicate quickly:

| # | Moat | Defensibility | Why competitors can't |
|---|------|--------------|---------------------|
| 1 | **Native Russian voice quality** | HIGH | Requires Russian-native founder to evaluate prosody, slang, diminutives. English-speaker founders can't tell if "Жорик" sounds right. |
| 2 | **Russian name transcription** | HIGH | STT accuracy for Людмила/Жорик/Матвей requires language-specific tuning. Competitors don't have Russian test corpus. |
| 3 | **EN↔RU mid-call switching** | HIGH | Caller says "my name is John, мне нужен Михаил" — must handle seamlessly. No competitor does this. |
| 4 | **Cultural greetings** | MEDIUM | "Здравствуйте" vs "Привет" based on time/formality. Russian business phone etiquette differs from American. |
| 5 | **Casual Russian time expressions** | MEDIUM | "После обеда", "ближе к вечеру", "на днях" — must parse into approximate times. |
| 6 | **Telegram-first notifications** | MEDIUM | Russian SMB owners live in Telegram. Competitors push email/SMS. |
| 7 | **Russian holiday awareness** | LOW | Business hours adjust for Russian Orthodox holidays (Jan 7, etc.) that many Brighton businesses observe. |
| 8 | **Separately-authored Russian landing** | MEDIUM | Not i18n — different tone, different trust signals, different FAQ. Competitors at best machine-translate. |
| 9 | **Russian Telegram support** | MEDIUM | @TimkaSupport in Russian. Competitors offer English email/chat only. |
| 10 | **Brooklyn founder trust** | HIGH | "Основатель из Брайтона, говорит по-русски, можно позвонить" — local trust in Russian community. Not replicable by VC-funded SF company. |

**Summary:** Moats 1-3 are technical (hard to replicate without Russian-speaking engineering). Moats 4-9 are operational (require cultural understanding). Moat 10 is personal (founder identity). Together they create a niche that's too small for Rosie/Synthflow to target but large enough for a solo founder.

---

## A.6 — Synthesis

### 5 Patterns We're Right About

1. **Call forwarding > BYOC/porting.** Rosie proved it. 1700 customers with zero porting. Customers keep their number, forward to ours. <5 min onboarding.

2. **Message-taker positioning works.** Every successful SMB voice AI is a glorified answering machine + smart transfer. Not scheduling, not booking, not CRM — just: take the message, notify the owner, transfer if available.

3. **Single price tier for launch.** Rosie: $49 one tier. Goodcall: $59 one tier. Complexity comes later. Our $79/500min is competitive.

4. **Pipeline architecture (not monolith).** Pipecat, LiveKit Agents — all use processor pipeline. Small modules, clear boundaries. Our 2393-line server.js is the anti-pattern.

5. **Russian = real gap in market.** Zero turnkey competitors serve Russian-speaking SMBs. Only Retell has Russian as a dev API. This is our wedge.

### 3 Anti-Patterns to Avoid

1. **God file / monolith backend.** Our server.js (2393 lines) is exactly what Pipecat's 671-line handler shows as the failure mode. Pipeline processors must stay <200 lines each.

2. **Single-vendor voice dependency.** Rosie depends entirely on Bland AI. If Bland changes pricing or drops a feature, Rosie dies. We should own the voice orchestration layer even if we start with one provider.

3. **Language support without quality verification.** Synthflow "lists" Russian. Quality unverified. We must not ship without the blind listening test (A.4). Marketing claims ≠ production quality.

### Stack Convergence

Research points to this stack shape (final choice in Phase C after A.4 gate):

| Layer | If OpenAI Russian passes | If hybrid needed |
|-------|------------------------|-----------------|
| Telephony | Telnyx (locked) | Telnyx (locked) |
| Voice Pipeline | Direct SIP → OpenAI Realtime | Custom middleware (Deepgram STT + GPT-4.1 + ElevenLabs TTS) |
| Backend | TypeScript + Hono/Fastify | Same |
| Database | Supabase (RLS, auth) | Same |
| Workflows | Code-based (Inngest or BullMQ) | Same |
| Frontend | Next.js (existing design preserved) | Same |
| Notifications | Telegram API + Resend fallback | Same |

**The A.4 voice quality gate is THE decision that unlocks everything else.** Until Eugene listens, we can't finalize the voice pipeline, which cascades to middleware, hosting, and cost.

---

## Open Questions for Eugene — CRITICAL

1. **TELNYX $1K MINIMUM** — Managed Accounts require $1,000/mo commit. See DEC-006. Recommend: start on pay-as-you-go with app-layer isolation.
2. **Voice quality test** — 30 min. Test OpenAI Realtime Russian + ElevenLabs Russian. GO/NO-GO gate. See DEC-007.
3. **LLC filing** — Deferred to Phase 0.5 (your edit). Confirm: comfortable operating as sole proprietor during build?
4. **n8n decision** — 37 workflows exist. Willing to rewrite as TypeScript (Inngest/BullMQ)?
5. **The "30K line file"** from v6 prompt — server.js is 2393 lines now. Was it bigger before, or was 30K total LOC?
