# QA Scenarios — TimkaMe v6

> Phase F deliverable. All 10 pass before ANY real-prospect demo.
> Re-run after any voice pipeline, prompt, or transfer change.
> Compiled 04/18/2026.

## О чём этот файл (RU)

10 тестовых сценариев, которые нужно пройти реальными звонками перед любой демонстрацией клиентам. Каждый сценарий = настоящий звонок с телефона, не симуляция. Евгений проводит тесты лично. Покрывают: звонок на английском, быструю русскую речь с именами, прерывание бота, тишину, шум, запрос на переключение на человека, два одновременных звонка, плохую связь, обрыв интернета, и 2-минутный монолог. Все 10 должны пройти — частичный результат = провал.

---

## How to run

Each scenario requires a real phone call to the TimkaMe number. No simulated audio — real phone, real network conditions. Eugene runs these personally.

**Equipment needed:**
- Two phones (personal + second device)
- Quiet room for clean audio tests
- Noisy environment for scenario #5 (café, street, car)
- Stopwatch app for timing

**Pass/Fail:** Each scenario has explicit pass criteria. Partial pass = fail.

---

## Scenario 1: English-only caller

**Setup:** Call from English-only speaker (or Eugene speaking English with American accent).

**Script:** "Hi, I'd like to speak with someone about getting my car repaired. My name is John Smith, my number is 718-555-1234."

**Pass criteria:**
- [ ] Bot detects English, responds in English
- [ ] Bot collects: name (John Smith), phone (718-555-1234), reason (car repair)
- [ ] Transcript accurate (name, phone, reason correctly captured)
- [ ] Telegram notification sent within 30 seconds of call end
- [ ] Notification includes all collected info

**Specifically tests:** Language detection, English voice quality

---

## Scenario 2: Fast Russian speech with accent

**Setup:** Eugene speaks Russian quickly, with natural Brooklyn-Russian cadence.

**Script:** "Алло, здравствуйте. Меня зовут Людмила Сергеевна Петрова, мне нужно записаться на приём. Мой номер 917-555-6789. Перезвоните мне пожалуйста после обеда."

**Pass criteria:**
- [ ] Bot responds in Russian
- [ ] "Людмила Сергеевна" transcribed correctly (not "Ludmila Sergeevna" or garbled)
- [ ] Phone number 917-555-6789 captured correctly
- [ ] "После обеда" understood as approximate time (afternoon)
- [ ] Transcript readable and accurate

**Specifically tests:** Russian STT accuracy, name transcription, colloquial time expressions

---

## Scenario 3: Caller interrupts mid-sentence

**Setup:** Eugene calls, waits for bot to start speaking, then interrupts mid-word.

**Script:**
1. Let bot say greeting
2. Interrupt at "Чем могу..." with: "Мне нужен Михаил, срочно!"
3. If bot restarts greeting, interrupt again: "Михаил! Позовите его!"

**Pass criteria:**
- [ ] Bot stops speaking within 500ms of interruption
- [ ] No talkover (bot and caller speaking simultaneously >1 sec)
- [ ] Bot processes the interruption content ("нужен Михаил")
- [ ] Smart transfer initiated OR message taken for Михаил
- [ ] No duplicate greeting after interruption

**Specifically tests:** Interruption handling, turn detection, no talkover

---

## Scenario 4: Silent caller (10+ seconds)

**Setup:** Call, stay silent after greeting.

**Script:** Say nothing for 15 seconds after bot greeting.

**Pass criteria:**
- [ ] Bot prompts at ~10 sec: "Вы меня слышите?" or equivalent
- [ ] If still silent at ~20 sec: bot takes message ("Похоже, связь прервалась. Если вы хотите оставить сообщение, перезвоните.")
- [ ] Call ends gracefully (not hanging forever)
- [ ] Partial transcript saved (greeting + silence + prompt)
- [ ] Notification sent with "silent caller" note

**Specifically tests:** Silence handling, timeout behavior, graceful degradation

---

## Scenario 5: Loud background noise

**Setup:** Call from noisy environment (street, café with music, car with windows open).

**Script:** "Здравствуйте, мне нужен ремонт стиральной машины. Мой номер 347-555-0000. Можно завтра утром?"

**Pass criteria:**
- [ ] Bot understands the request despite noise
- [ ] Phone number captured correctly
- [ ] No panic-transcribe (random noise words in transcript)
- [ ] Bot asks for clarification if genuinely unclear (not just guessing)
- [ ] Transcript quality sufficient for owner to understand caller's need

**Specifically tests:** Noise resilience, STT accuracy in non-ideal conditions

---

## Scenario 6: Asks for human immediately

**Setup:** Call and immediately demand to speak to a person.

**Script:** "Здравствуйте, мне нужен живой человек. Переключите на менеджера."

**Pass criteria:**
- [ ] Smart transfer initiated within 5 seconds of request
- [ ] No arguing ("Может я смогу помочь..." = FAIL)
- [ ] Owner's phone rings (20-sec ring timeout)
- [ ] If owner answers → call connected
- [ ] If owner doesn't answer → bot returns: "К сожалению, сейчас не могу переключить. Могу принять сообщение?"
- [ ] Notification sent regardless of transfer outcome

**Specifically tests:** Smart transfer, no resistance to human request

---

## Scenario 7: Second call during active call

**Setup:** Call from phone A, while call is active, call from phone B.

**Pass criteria:**
- [ ] Phone B gets proper handling (busy signal, queue, or separate AI instance)
- [ ] Phone A's call is NOT interrupted or degraded
- [ ] Both calls produce separate transcripts
- [ ] Both callers get notifications sent to owner
- [ ] No errors in logs from concurrent call handling

**Specifically tests:** Concurrent call handling, no cross-call contamination

---

## Scenario 8: Poor connection (packet loss)

**Setup:** Call from area with weak signal, or use phone in elevator/basement.

**Script:** "М...зовут...Ан...ей. Ну...р 718...55...23. П...звоните з...тра."

**Pass criteria:**
- [ ] Bot doesn't crash or hang
- [ ] Bot asks for clarification: "Извините, плохая связь. Можете повторить имя?"
- [ ] If caller repeats → captures what it can
- [ ] Partial transcript saved (best-effort)
- [ ] Notification sent with whatever was captured
- [ ] "Poor connection" flag in transcript

**Specifically tests:** Graceful degradation, retry comprehension, no crash

---

## Scenario 9: Internet drops mid-call

**Setup:** Start call normally, then enable airplane mode on the phone mid-conversation (or disconnect WiFi if using WiFi calling).

**Pass criteria:**
- [ ] Call ends without server crash
- [ ] Partial transcript saved (everything up to disconnection)
- [ ] Notification sent to owner with partial info
- [ ] System recovers — next call works normally
- [ ] No zombie connections or resource leaks

**Specifically tests:** Ungraceful disconnection handling, resource cleanup

---

## Scenario 10: 2-minute monologue

**Setup:** Call and deliver a long, continuous message without pausing.

**Script:** 2 minutes of continuous speaking — describe a complex car repair problem, include name, phone number, multiple issues, preferred timing, budget mention.

**Pass criteria:**
- [ ] Full transcript captured (not truncated)
- [ ] Bot doesn't interrupt during sustained speech (good turn detection)
- [ ] Summary in notification captures key points (name, phone, main issues)
- [ ] Call duration correctly recorded
- [ ] Billable minutes calculated correctly (2 min = 2 billable minutes)

**Specifically tests:** Long-form speech handling, transcript completeness, billing accuracy

---

## Re-run Triggers

Run ALL 10 scenarios after any of:
- Voice provider change (OpenAI Realtime settings, TTS/STT swap)
- Prompt template change (greeting, behavior instructions)
- Smart transfer logic change
- Concurrent call handling change
- New Telnyx number provisioned

Run scenarios 1-3 specifically after:
- Russian voice quality tune (different voice, different model)
- Language detection logic change
