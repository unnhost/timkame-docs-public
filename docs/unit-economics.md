# Unit Economics — TimkaMe v6

> Phase F deliverable. Revisit monthly with real data. No "feel" math. Compiled 04/18/2026.

## Краткое резюме (RU)

Юнит-экономика при тарифе $79/мес, 500 минут включено, $0.15/мин превышение.

**Главные цифры:**
- **Себестоимость звонка** (2.5 мин средний): ~$0.18 (Telnyx ~$0.004/мин + OpenAI ~$0.06/мин + хранение)
- **Себестоимость клиента** при типичном использовании (200 мин/мес): ~$18.50/мес
- **Маржа:** $60.50 на клиента (77%) при типичном использовании
- **Break-even:** 3 платящих клиента покрывают все фиксированные расходы
- **Фиксированные расходы** после запуска: ~$120-150/мес (хостинг + страховка + домен)
- **LTV клиента:** ~$1,138 (18 мес × $79 минус 20% на churn)
- **CAC:** ~$50 (время Евгения на outreach по Brighton Beach)

**Ключевые пороги:**
- 3 клиента = самоокупаемость
- 7 клиентов = комфортный запас
- 51 клиент = можно отказаться от одной смены в больнице
- 127 клиентов = полный переход на TimkaMe

**Даже при дорогом OpenAI ($0.15/мин) маржа остаётся >50%.** Ценообразование устойчиво.

---

## Cost Per Call (loaded)

### Voice processing
| Component | Cost | Notes |
|-----------|------|-------|
| Telnyx inbound | ~$0.004/min | Voice API $0.002 + SIP trunking fee (~$0.002) |
| Telnyx outbound (smart transfer) | ~$0.008/min | Outbound rates higher than inbound |
| OpenAI Realtime | ~$0.06/min | Estimated (audio in + out + processing) |
| **Total voice per minute** | **~$0.07/min** | Conservative (includes transfer leg possibility) |

### Per-call average (assuming 2.5 min avg call)
| Item | Cost |
|------|------|
| Voice processing (2.5 min × $0.07) | $0.175 |
| Telegram notification (API call) | ~$0.00 |
| Transcript storage (Supabase) | ~$0.001 |
| **Total per call** | **~$0.18** |

---

## Cost Per Customer Per Month

### At 500 minutes (included tier usage)

| Item | Cost | Notes |
|------|------|-------|
| Voice (500 min × $0.07) | $35.00 | If customer uses all 500 min |
| Phone number (Telnyx) | $1.00 | Monthly DID cost |
| Hosting (apportioned) | $3.00 | At 10 customers: $30 hosting / 10 |
| Observability (apportioned) | $0.50 | Sentry free, synthetic tests ~$5/mo / 10 |
| **Total per customer (full usage)** | **~$39.50** | Worst case |

### At 200 minutes (typical usage, 40% utilization)

| Item | Cost |
|------|------|
| Voice (200 min × $0.07) | $14.00 |
| Phone number | $1.00 |
| Hosting (apportioned) | $3.00 |
| Observability (apportioned) | $0.50 |
| **Total per customer (typical)** | **~$18.50** |

---

## Revenue Per Customer

| Source | Amount |
|--------|--------|
| Subscription | $79.00 |
| Overage (avg, if >500 min) | ~$0-15 |
| **Total revenue per customer** | **$79-94** |

---

## Contribution Margin

| Scenario | Revenue | Cost | Margin | Margin % |
|----------|---------|------|--------|----------|
| Typical usage (200 min) | $79 | $18.50 | **$60.50** | 77% |
| Full usage (500 min) | $79 | $39.50 | **$39.50** | 50% |
| Heavy overage (700 min) | $109 | $50.00 | **$59.00** | 54% |

---

## Fixed Monthly Costs

### Pre-Phase 0.5 (during development)

| Item | Cost |
|------|------|
| Hetzner server (or equivalent) | $15-30 |
| Domain (timkame.com) | ~$1.25 |
| Free tools (Supabase, Sentry, PostHog, GitHub) | $0 |
| **Total pre-0.5** | **~$16-31** |

### Post-Phase 0.5 (after LLC, before customers)

| Item | Cost |
|------|------|
| Hosting | $15-30 |
| Domain | ~$1.25 |
| Insurance (GL + Cyber) | ~$80 |
| Registered agent | ~$10 |
| **Total post-0.5** | **~$106-121** |

### Steady state (with customers)

| Item | Cost |
|------|------|
| Hosting | $15-30 |
| Domain | ~$1.25 |
| Insurance | ~$80 |
| Registered agent | ~$10 |
| Tools (may upgrade from free tiers) | $0-50 |
| Stripe fees (2.9% + $0.30 per invoice) | Per-transaction |
| **Total fixed** | **~$106-171** |

---

## Break-Even Analysis

### At typical usage (200 min/customer, $60.50 contribution)

| Scenario | Fixed cost | Break-even customers |
|----------|-----------|---------------------|
| Pre-0.5 (development) | $25/mo | N/A (no revenue) |
| Post-0.5 (launched) | $120/mo | **2 customers** |
| Steady state | $150/mo | **3 customers** |

**3 paying customers covers all fixed costs at typical usage.**

---

## Key Thresholds

| Milestone | MRR | Customers | What it means |
|-----------|-----|-----------|---------------|
| **Self-sustaining** | $150 | 3 | Covers all fixed costs |
| **Comfortable** | $500 | 7 | Covers costs + small buffer |
| **Drop one shift** | $4,000 | ~51 | Replaces 1 nursing shift income |
| **Full-time viable** | $10,000+ | ~127 | Sustainable full-time with growth |

**Reality check:** Going from 0 → 3 customers is the hardest step. 3 → 10 is about product-market fit. 10 → 50 requires marketing. 50 → 127 requires hiring or automation.

---

## LTV (Lifetime Value)

| Assumption | Value |
|-----------|-------|
| Average retention | 18 months |
| Monthly revenue | $79 |
| Gross LTV | $79 × 18 = $1,422 |
| Churn discount (-20%) | **~$1,138** |

---

## CAC (Customer Acquisition Cost)

| Method | Cost |
|--------|------|
| Brighton walk-ins (Eugene's time) | 2 hrs × $30/hr opportunity cost = $60 |
| Facebook group posts (Eugene's time) | 1 hr × $30/hr = $30 |
| Personal network referral | $0 + $20 referral credit |
| **Blended CAC (first 10)** | **~$50** |

**CAC ceiling:** LTV/3 = $380. Solo founder CAC mostly time. At $50 blended, we're well within ceiling.

---

## Stripe Fees

| Item | Fee |
|------|-----|
| Subscription ($79) | 2.9% + $0.30 = $2.59 |
| Net revenue after Stripe | $76.41 |
| Overage invoice (if $30) | 2.9% + $0.30 = $1.17 |

Stripe fees are small relative to contribution margin. Not worth optimizing until >100 customers.

---

## Sensitivity Analysis

### What if OpenAI Realtime costs more than estimated?

| OpenAI cost/min | Cost per customer (200 min) | Margin |
|----------------|---------------------------|--------|
| $0.04 | $12.50 | 84% |
| $0.06 (baseline) | $18.50 | 77% |
| $0.10 | $26.50 | 66% |
| $0.15 | $36.50 | 54% |

Even at $0.15/min voice cost, margin is >50%. Pricing is resilient.

### What if average usage is higher than 200 min?

| Avg usage | Cost per customer | Margin |
|-----------|------------------|--------|
| 100 min | $11.50 | 85% |
| 200 min (baseline) | $18.50 | 77% |
| 400 min | $32.50 | 59% |
| 500 min (full) | $39.50 | 50% |

At 50% margin (full 500 min usage), still profitable. Overage at $0.15/min adds revenue to offset.

---

## Monthly Review Checklist

- [ ] Actual vs estimated cost per call
- [ ] Actual avg call duration
- [ ] Actual avg usage per customer (min)
- [ ] Actual Telnyx bill vs estimate
- [ ] Actual OpenAI bill vs estimate
- [ ] Stripe fee total
- [ ] Fixed costs changed?
- [ ] Break-even still accurate?
