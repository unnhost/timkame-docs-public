# Weekly Review — TimkaMe v6

> Заполняется в конце каждой недели. Помогает отслеживать дрифт от roadmap.

---

## Template

```
### Week ending: YYYY-MM-DD

**Hours worked:** N

**Planned vs actual:**
- [план] → [что реально сделали / что сдвинулось]

**Задачи что постоянно откладываются:**
- [список задач которые переносятся из недели в неделю]

**Decision:** продолжаем по плану / корректируем roadmap / пауза
```

---

### Week ending: 2026-04-20

**Hours worked:** ~20

**Planned vs actual:**
- Week 5-6 Priority 1 (custom prompts) → done (PR #19)
- Week 5-6 Priority 2 (forwarding wizard) → not started, pushed to next week
- Voice quality tuning → hit ceiling (DEC-021), reverted PR #12, accepted current state

**Задачи что постоянно откладываются:**
- Webhook signature verification (третья неделя подряд)
- Legal docs drafts (ToS, Privacy, AUP)

**Decision:** продолжаем по плану — forwarding wizard + notifications next

---

### Week ending: 2026-04-21

**Hours worked:** не считали, много продуктивных сессий

**Planned vs actual:**
- v6 deploy на Hetzner → done (https://v6.timkame.com рядом с v5)
- Первый полный call flow → done (caller → OpenAI Realtime → Telegram notification)
- Voice quality tuning PRs #9-#18 → done (hybrid VAD, coral/ash voices, bilingual mode)
- Custom prompts PR #19 → done (DEC-022, bot использует business profile из БД)
- Демо-клиент засеян: Автосервис EuroBMW
- Docs restructure PR #20 → done (NEXT-SESSION primary, TIMELINE archive, Session Modes, WEEKLY-REVIEW)
- Roadmap очищен от часовых оценок
- Cherry-pick план v5 → v6 frontend запущен (Option B approved)
- Phase 1 Week 3-4 и Week 5-6 идут параллельно, не последовательно — velocity выше плана

**Задачи что постоянно откладываются:**
- Webhook signature verification (2-я сессия подряд, всё ещё bypass)
- Forwarding wizard (запланирован, не начат)
- Notification settings UI (запланирован, не начат)

**Decision:** продолжаем по плану. Фокус следующей сессии — cherry-pick frontend (Phase 1 дизайн-система), webhook signature verification параллельно.

**Примечание про velocity:** С Claude Code скорость в разы выше исходной оценки 250-300 часов / 16-20 недель. Оценки часов убраны из roadmap. Фокус на порядке и зависимостях.

---

### Week ending: 2026-04-21 (session 2)

**Hours worked:** full day session

**Planned vs actual:**
- v5 frontend full port → done (PR #26: 150 files, 29,900 lines)
- v5 landing HTML as-is → done (PR #28-29: 72 pages with cat mascot)
- Unified content blocks → done (PR #30-31: change price once, updates everywhere)
- Dashboard audit + cleanup → done (PR #33: 22 dead links removed)
- Signup button fix → done (PR #34: Turnstile token bypass)
- Signup API v6 schema rewrite → done (PR #35: organizations + user_profiles)
- Resend email integration → done (DEC-033: branded templates with cat mascot)
- Auth confirm flow fixed → done (session cookies on redirect)
- Logout via /logout page → done (workaround for broken dashboard JS)
- Webhook signatures PR #25 → ready to merge (CI green, not deployed)

**PRs merged this session:** #24, #26, #27, #28, #29, #30, #31, #32, #33, #34, #35, #36 + 5 direct main commits

**Задачи что постоянно откладываются:**
- Webhook signature enforcement (PR ready, not merged — needs logging-only rollout)
- Forwarding wizard (3rd week)
- Dashboard logout button (v5 JS breaks it)
- Password reset e2e test

**Decision:** signup flow complete end-to-end. Pricing model approved. Next: merge webhook PR, fix dashboard, implement pricing v2.
