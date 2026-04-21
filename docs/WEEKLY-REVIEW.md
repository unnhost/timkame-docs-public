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
