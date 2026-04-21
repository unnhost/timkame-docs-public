# Voice Quality GO/NO-GO Gate — TimkaMe v6

> Phase H.1 deliverable. Compiled 04/18/2026.
> **Status: ✅ PASSED — OpenAI Realtime, confirmed 04/18/2026.**

## Результат теста (RU)

Тест пройден. OpenAI Realtime показал хорошее качество русского голоса. Евгений подтвердил лично. Стек зафиксирован: прямой Telnyx SIP → OpenAI Realtime. Никакого Pipecat middleware, никакого hybrid pipeline. Простейший вариант с наименьшей задержкой (~450ms).

---

## Result

**Winner:** OpenAI Realtime
**Date tested:** 04/18/2026
**Tested by:** Eugene (native Russian speaker, founder)
**Verdict:** PASSED — Russian voice quality confirmed as good enough to ship
**Stack decision:** Direct Telnyx SIP → OpenAI Realtime. No middleware.

---

## Purpose

This gate determines whether the Russian voice AI quality is good enough to ship. If no provider passes, we stop and pivot. No shipping on hope.

## Test Protocol

**Duration:** 30 minutes.
**Who:** Eugene (native Russian speaker, founder).
**Format:** Blind test — Eugene doesn't know which provider is speaking.

### Test phrases (must include)

**Russian names (the hardest test):**
- Людмила Сергеевна (patronymic, formal)
- Жорик (informal diminutive)
- Матвей (common male name)
- Светлана (common female name)
- Екатерина Дмитриевна (full formal)

**Business conversation snippets:**
- "Здравствуйте, вы позвонили в автосервис. Чем могу помочь?"
- "К сожалению, Михаил сейчас занят. Могу ли я принять сообщение?"
- "Ваш номер +1 (718) 555-1234, верно?"
- "Запись на среду после обеда, хорошо?"

**EN↔RU code-switching:**
- "My name is John Smith, мне нужен Михаил"
- "I need to schedule an appointment, можно на завтра?"

### Scoring criteria (1-10 each)

| Criterion | Weight | What 7/10 means |
|-----------|--------|-----------------|
| Natural prosody | 25% | Sounds like a real Russian person, not a robot. Stress on correct syllables. |
| Name pronunciation | 25% | Людмила, Жорик, Екатерина pronounced correctly (stress, vowel reduction, palatalization) |
| Foreign accent in TTS | 20% | Minimal English accent when speaking Russian. No "American robot speaking Russian." |
| STT accuracy | 15% | Correctly transcribes Жорик (not "Jorik"), Екатерина (not "Yekaterina"), phone numbers |
| EN↔RU switching | 15% | Handles mid-sentence language switches without breaking or long pauses |

### Pass threshold

**≥1 provider must score 7/10 weighted average.** If no provider passes:

### Failure options

| Option | Description | Timeline |
|--------|-------------|----------|
| **(a) Wait** | Re-test in 3 months. OpenAI, Google, ElevenLabs all improving Russian rapidly. | 3 months |
| **(b) Hybrid best-of-breed** | Deepgram STT (best Russian transcription) + GPT-4.1 (best reasoning) + ElevenLabs TTS (best Russian voice). More complex pipeline (Pipecat middleware required) but best audio quality. | +2-3 weeks build time |
| **(c) English beachhead** | Pivot: serve English-speaking immigrant SMBs first. "Your customers call in English, your AI answers in English." Russian becomes Phase 3 addition. | Immediate |

**Eugene's call.** Not a technical decision — it's a product positioning decision.

## Providers to Test

### Priority 1: OpenAI Realtime
- **Why first:** If this passes, we get direct Telnyx SIP routing. Simplest stack. Lowest latency (150-200ms). No middleware.
- **How to test:** OpenAI Playground → Realtime mode → Russian conversation
- **What to watch:** Prosody, name pronunciation, accent

### Priority 2: ElevenLabs (TTS only)
- **Why:** Reportedly best Russian TTS quality. But TTS-only — need separate STT.
- **How to test:** ElevenLabs voice playground → Russian text
- **If best TTS:** Pair with Deepgram Nova-3 STT. Requires middleware pipeline.

### Priority 3: Deepgram Nova-3 (STT only)
- **Why:** Reportedly best Russian STT accuracy. But STT-only — need separate TTS.
- **How to test:** Upload Russian audio clips → check transcription accuracy
- **Key test:** Does it transcribe "Жорик" as "Жорик" or "Джорик" or "Jorик"?

### Priority 4: Gemini Live / Ultravox
- **Why:** Both claim 90+ / 26 languages. Russian quality unverified.
- **How to test:** Google AI Studio (Gemini), Ultravox playground
- **Expectation:** Probably worse than OpenAI/ElevenLabs but worth 5 min check

## Decision Matrix

| If... | Then... | Stack impact |
|-------|---------|-------------|
| OpenAI Realtime ≥7/10 | **Direct SIP.** No middleware. Simplest. | Telnyx SIP → OpenAI. ~$0.06/min voice. |
| OpenAI <7 but ElevenLabs+Deepgram ≥7 | **Hybrid pipeline.** Best-of-breed. | Middleware required (custom or Pipecat). ~$0.09/min voice. |
| All <7 | **STOP.** Wait or pivot. | No voice code written until resolved. |

## After Test

Eugene fills in this table and we proceed:

| Provider | Prosody (/10) | Names (/10) | Accent (/10) | STT (/10) | Switching (/10) | Weighted |
|----------|-------------|-------------|-------------|-----------|----------------|----------|
| OpenAI Realtime | | | | | | |
| ElevenLabs TTS | | | N/A | N/A | | |
| Deepgram STT | N/A | N/A | N/A | | N/A | |
| Gemini Live | | | | | | |
| Ultravox | | | | | | |

**Winner:** _______________
**Stack decision:** _______________
**Date tested:** _______________
