# Security Roadmap — TimkaMe v6

> Phase G deliverable. Compiled 04/18/2026.

## Краткое резюме (RU)

План безопасности в три этапа.

**Day 1 (до первого клиента):**
- Все секреты через Doppler (никаких `.env` на сервере)
- HTTPS везде, TLS 1.2+
- RLS на каждой таблице + тест на изоляцию между тенантами
- Zod-валидация на каждом API endpoint
- Подпись webhook от Telnyx и Stripe проверяется
- PII не пишется в логи (телефоны — только последние 4 цифры)
- Аудит-лог на чувствительные действия
- Runbook на инциденты (NY SHIELD Act — 72 часа на уведомление о breach)

**Month 3:**
- OWASP ZAP скан всех публичных endpoints
- Renovate/Dependabot для обновления зависимостей
- Чеклист готовности к SOC 2 Type 1 (без сертификации)

**Month 9+:**
- SOC 2 Type 2 если попросит enterprise-клиент
- Ротация секретов по расписанию

**Не в MVP:** multi-region, HSM, HIPAA, FedRAMP, bug bounty. Это нужно только при enterprise-клиентах.

---

## Day 1 — Before First Customer (Phase 0.5 time)

Everything below must be verified before any customer handles real calls.

### Secrets management
- [ ] Doppler (or Infisical) configured for all environments
- [ ] No `.env` files on production server
- [ ] All secrets rotated from development values
- [ ] GitHub Actions secrets stored in repo settings, not in workflow files

### Transport security
- [ ] HTTPS everywhere — no HTTP endpoints, HSTS enabled
- [ ] TLS 1.2+ minimum
- [ ] Certificate auto-renewal (Let's Encrypt / Cloudflare)

### Database security
- [ ] RLS enabled on every table
- [ ] Cross-tenant isolation test written and passing (attempt to read org_B data with org_A token → 0 rows)
- [ ] Supabase PITR (Point-in-Time Recovery) enabled
- [ ] Database connection via SSL only
- [ ] No public schema exposure — all access through API or RPC

### API security
- [ ] Zod input validation on every endpoint
- [ ] Rate limiting: 100 req/min per IP, 1000 req/min per org
- [ ] CORS locked to `timkame.com` + `localhost` (dev only)
- [ ] Stripe restricted API keys (only permissions needed)
- [ ] No `any` types in request/response handling

### Webhook security
- [ ] Telnyx webhook signature verification (HMAC-SHA256)
- [ ] Stripe webhook signature verification (`stripe.webhooks.constructEvent`)
- [ ] Replay protection (reject events >5 min old)
- [ ] Idempotency keys for state-changing webhook handlers

### Audit & logging
- [ ] Audit log on sensitive actions: login, password change, prompt edit, number change, billing change
- [ ] PII sanitized in all logs (phone → `***-***-1234`, names → never logged)
- [ ] Log retention: 30 days application logs, 90 days audit logs

### Incident response
- [ ] Incident runbook written (`docs/incidents/TEMPLATE.md`)
- [ ] P0 alert → auto-creates incident file
- [ ] Breach notification template ready (NY SHIELD Act: 72 hours)
- [ ] Contact list: Eugene (phone), Supabase support, Telnyx support, Stripe support

---

## Month 3 — Hardening

### Vulnerability scanning
- [ ] OWASP ZAP scan on all public endpoints
- [ ] Fix all Critical + High findings
- [ ] Renovate bot or Dependabot enabled for dependency updates
- [ ] `npm audit` integrated into CI — fail on Critical

### SOC 2 Type 1 readiness (checklist only, not certification)
- [ ] Access control policies documented
- [ ] Change management process documented
- [ ] Incident response tested (tabletop exercise)
- [ ] Data classification (PII, sensitive, public) documented
- [ ] Vendor risk assessment for: Telnyx, OpenAI, Supabase, Stripe

### Operational security
- [ ] Admin access requires 2FA
- [ ] SSH key-only access to any servers
- [ ] GitHub branch protection: require review, require CI pass
- [ ] Secret rotation schedule documented (90-day minimum)

---

## Month 9+ — Scale Security

### If enterprise customers request SOC 2:
- SOC 2 Type 2 certification (~$15-30K)
- Dedicated security engineering hire or contractor
- Annual penetration test by third party

### If handling >1000 customers:
- Secret rotation automation
- Separate staging environment with anonymized data
- Database encryption at rest (Supabase provides by default)
- WAF (Cloudflare) on all public endpoints

---

## Out of MVP Scope (explicitly excluded)

- Multi-region failover
- HSM (Hardware Security Modules)
- HIPAA / BAA compliance
- FedRAMP
- Dedicated security team
- Bug bounty program
- On-premise deployment option

These are real requirements only if enterprise customers with specific compliance needs appear. Don't build for hypothetical compliance.
