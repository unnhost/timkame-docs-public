# Next Session — TimkaMe v6

## Текущее состояние

v6 задеплоен на https://v6.timkame.com. Работает: Supabase Auth, RLS (14 тестов), Telnyx SIP → OpenAI Realtime (direct, без middleware), кастомные промпты на уровне организации (PR #19), dashboard business-profile. Демо: автосервис EuroBMW (BMW/Mercedes, Brooklyn), звонок на +17182151936 → AI знает про сервис, часы, услуги, цены, спрашивает марку авто. Telegram-нотификации с саммари работают. Public docs mirror: https://raw.githubusercontent.com/unnhost/timkame-docs-public/main/ (auto-synced via GitHub Actions).

## Следующие задачи

1. **Forwarding wizard** — настройка переадресации звонков в dashboard (Week 5-6 part 2) — ~8 hrs
2. **Notification settings** — email + Telegram preferences в dashboard — ~4 hrs
3. **Webhook signature verification** — Telnyx webhook verify (всё ещё bypass) — ~2 hrs

## Работает в проде

- Supabase Auth (signup/login/logout, cookie sessions + JWT API)
- RLS tenant isolation (14/14 тестов)
- Telnyx SIP → OpenAI Realtime (direct, no middleware)
- Кастомные промпты: business description, hours, services, pricing, collect fields, instructions
- Dashboard: /dashboard/business-profile — редактирование всех полей, preview приветствия
- Telegram notifications с call summary
- Bilingual mode (russian_only | english_only | bilingual)
- Hybrid VAD: null для greeting, semantic_vad для conversation
- Configurable voice via OPENAI_VOICE env (текущий: ash)
- CD: GitHub Actions → Hetzner (PM2 + nginx + Let's Encrypt)

## Известные проблемы

1. **Webhook signature verification bypass** — Telnyx webhooks не верифицируются, security risk — HIGH
2. **Voice tuning ceiling** — hybrid VAD is max quality without middleware (DEC-021) — LOW, accept for now
3. **Legal docs** — ToS, Privacy Policy, AUP не написаны — MEDIUM, нужны до pre-launch
4. **DMARC/SPF/DKIM** — email auth не настроен — LOW

## Блокеры

Нет.

---
Last updated: 2026-04-20T22:00:00-04:00
