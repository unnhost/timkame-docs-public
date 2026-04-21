# First Customer Runbook — TimkaMe v6

> Phase F deliverable. Compiled 04/18/2026.

## Краткое резюме (RU)

Пошаговый план работы с первым клиентом — от устного согласия до отзыва через 30 дней.

**Ключевые шаги:**
- **День 0:** Отправить ссылку на оплату через Telegram. Назначить onboarding-звонок (30 мин, лично Евгений).
- **Onboarding-звонок:** Евгений лично настраивает переадресацию, делает тестовый звонок, подключает Telegram-уведомления.
- **День 1, 3, 7:** Чекины через Telegram — как дела, что улучшить.
- **День 30:** Попросить отзыв (формат: «Имя, район, тип бизнеса + 2-3 предложения»).

**Обязательно перед первым клиентом:** Phase 0.5 завершён (LLC, страховка, Stripe live, юр. документы). Все 10 QA-сценариев пройдены.

**Первые 10 клиентов = VIP-обслуживание.** Личный номер Евгения, concierge setup, ручные follow-up. Это не масштабируется — и это намеренно.

---

## Prerequisites (before first verbal commit)

- [ ] Phase 0.5 COMPLETE — LLC filed, insurance active, Stripe live, attorney-reviewed legal docs
- [ ] All 10 QA scenarios passing
- [ ] Russian voice quality verified in production (not demo)
- [ ] Status page live and showing green
- [ ] Support channels tested (Telegram bot + email)

**If Phase 0.5 is not complete: DO NOT accept payment.** You can demo, but cannot charge.

## Day 0: Verbal Commit Received

**Within 1 hour:**
- [ ] Send Stripe payment link via Telegram (not email — Russian SMBs live in Telegram)
- [ ] Confirm: "Оплата прошла, спасибо! Давайте настроим — когда удобно поговорить?" (Payment received, thanks! Let's set up — when's a good time to talk?)

**If payment fails:** Follow up within 2 hours. Offer to walk through payment on call.

## Day 0-1: Onboarding Call (Eugene, concierge)

**Duration:** 30 min max.
**Format:** Phone call or Telegram voice call.
**Language:** Whatever the customer prefers (RU or EN).

### Call agenda:
1. **Intro (2 min):** "I'm Eugene, the founder. I'll personally set everything up."
2. **Business info (5 min):** Business name, type, hours, what callers typically ask
3. **Forwarding setup (10 min):** Walk through call forwarding for their specific carrier
4. **Test call (5 min):** Eugene calls the forwarded number, customer listens
5. **AI greeting customization (5 min):** Review default greeting, adjust to their business
6. **Telegram setup (3 min):** Connect their Telegram for notifications

### Forwarding scripts by carrier:
| Carrier | How to forward |
|---------|---------------|
| Verizon | *72 + [TimkaMe number] |
| AT&T | *72 + [TimkaMe number] |
| T-Mobile | **21*[TimkaMe number]# |
| Spectrum/landline | *72 + [TimkaMe number] |
| Google Voice | Settings → Calls → Forwarding |
| RingCentral | Settings → Call Forwarding → Add number |

### After call:
- [ ] Verify first test call transcript appeared in dashboard
- [ ] Verify Telegram notification delivered to customer
- [ ] Set follow-up reminder for Day 1, 3, 7, 30

## Day 1: First Check-In

**Via Telegram (not email):**

> "Привет! Как первый день с AI-ресепшн? Были ли звонки? Всё ли работает нормально?"
> (Hi! How's the first day with AI receptionist? Any calls? Everything working?)

**If issues:** Fix immediately. First customer issues are P0.

## Day 3: Second Check-In

**Via Telegram:**

> "Привет! Несколько дней прошло — как ощущения? Есть ли что-то, что хотелось бы изменить в приветствии или поведении бота?"
> (Hi! It's been a few days — how's it going? Anything you'd like to change in the greeting or bot behavior?)

**Common Day 3 requests:**
- Adjust greeting wording
- Change transfer timeout
- Add specific business info to bot knowledge
- Adjust notification format

## Day 7: Feedback Collection

**Via Telegram, structured:**

> "Неделю работаем вместе! 3 быстрых вопроса:
> 1. По шкале 1-10, насколько AI-ресепшн полезен?
> 2. Что работает лучше всего?
> 3. Что хотелось бы улучшить?"

Record answers in customer notes. Act on #3 within 48 hours if feasible.

## Day 30: Reference Ask

**Via Telegram:**

> "Месяц вместе! Спасибо что доверяете 🙏
> Могу ли попросить об одолжении? Напишете пару предложений о вашем опыте с TimkaMe? Это поможет другим владельцам бизнеса узнать о нас.
> Формат: Имя, район, тип бизнеса + 2-3 предложения. Пример: 'Михаил, Брайтон-Бич, автосервис — перестал пропускать звонки, клиенты довольны.'"

**If yes:** Add to testimonials content block (with written permission).
**If no:** Respect it. Don't ask again.

## Metrics to Track for Customer #1

| Metric | Track how |
|--------|-----------|
| Calls handled per day | Dashboard |
| Transfer success rate | Call records |
| Transcript accuracy (spot-check) | Read 3 transcripts/week |
| Notification delivery time | Telegram timestamps |
| Customer satisfaction | Day 7 + Day 30 surveys |
| First issue reported | Support log |
| First billing cycle success | Stripe |

## What to Learn from Customer #1

Customer #1 is the most valuable data source. Document everything:

- **Onboarding friction:** How long did setup actually take? Where did they get confused?
- **Carrier-specific issues:** Did call forwarding work on first try for their carrier?
- **Voice quality feedback:** Do they think the AI sounds natural?
- **Feature requests:** What do they actually ask for vs what we assumed they'd need?
- **Usage patterns:** How many calls/day? When? What kinds of requests?
- **Billing clarity:** Did they understand the invoice? Any confusion about overage?

Write findings in `docs/customer-learnings.md` after Day 30.
