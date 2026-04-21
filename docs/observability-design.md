# Observability Design — TimkaMe v6

> Phase C.7 deliverable. Budget <$10/mo at 0-10 customers. Compiled 04/18/2026.

---

## Stack

| Tool | Cost | Purpose |
|------|------|---------|
| **Sentry** (free tier) | $0 | Error tracking, 5K errors/mo + 10K transactions |
| **Upptime** (GitHub Actions) | $0 | Status page at status.timkame.com |
| **PostHog** (free tier) | $0 | Product analytics, 1M events/mo |
| **Telnyx synthetic calls** | ~$5/mo | Automated test calls every 15 min |
| **Structured logging** (Pino) | $0 | JSON logs, stored on server |
| **Total** | **~$5/mo** | |

---

## 1. Synthetic Test Calls

**What:** Automated phone call every 15 minutes to verify the voice pipeline is working end-to-end.

**How:**
1. Cron job (GitHub Actions or server cron) triggers every 15 min
2. Telnyx API makes outbound call to dedicated test number
3. Test script speaks: "Test call. Today is [date]. Repeat: apple banana cherry."
4. Voice pipeline processes the call
5. Verify: transcript contains "apple banana cherry" within 60 seconds
6. If 2 consecutive failures → P0 alert

**Cost:** ~$0.007/call × 4/hour × 24h × 30 days = ~$5/mo on Telnyx

**Test number:** Dedicated Telnyx number for synthetic tests only. Not assigned to any customer.

**Alert on failure:**
- 1 failure: silent log (network glitch, transient)
- 2 consecutive: P1 → Telegram alert to Eugene
- 3 consecutive: P0 → Telegram + SMS to Eugene

---

## 2. Status Page (Upptime)

**URL:** `status.timkame.com` (EN + RU)

**Monitors:**
| Endpoint | Check | Interval |
|----------|-------|----------|
| Backend API | `GET /health` → 200 | 5 min |
| Admin panel | `GET /admin` → 200 | 5 min |
| Voice pipeline | Synthetic call result | 15 min |
| Telnyx API | `GET /v2/available_phone_numbers` | 15 min |
| Supabase | Health check query | 5 min |

**Implementation:**
- GitHub repo: `timkame/status`
- Upptime runs as GitHub Actions (free)
- Results published to GitHub Pages → `status.timkame.com`
- Incident history visible to customers
- EN + RU status descriptions

---

## 3. Alert Hierarchy

| Level | Condition | Action | Channel |
|-------|-----------|--------|---------|
| **P0** | Service down (3+ synthetic fails, health check down >5 min) | Immediate response, incident file created | Telegram + SMS to Eugene |
| **P1** | Error rate >5% over 15 min, 2 synthetic fails | Response within 4h | Telegram to Eugene |
| **P2** | Warning threshold, single failures, slow latency | Log, review daily | Silent log (Sentry) |

### P0 auto-actions:
1. Telegram message: "🔴 P0: [service] down since [time]. Check immediately."
2. SMS backup: same message (in case Telegram is unreachable)
3. Auto-create incident file: `docs/incidents/YYYY-MM-DD-HH-summary.md`
4. Status page: auto-update to "Degraded" or "Down"

### P1 actions:
1. Telegram message: "🟡 P1: [service] error rate elevated. [details]"
2. No SMS (not urgent enough for SMS cost)

---

## 4. Call Quality Metrics (per tenant)

Tracked in database, visible in admin dashboard:

| Metric | How measured | Alert threshold |
|--------|-------------|----------------|
| **Time to first response** | Time from call connect to first AI speech | >3 sec = investigate |
| **Turn latency** | Time between user stops speaking and AI starts | >2 sec = poor experience |
| **Dropped call rate** | Calls ending in error / total calls | >5% = P1 |
| **Transfer success rate** | Successful transfers / transfer attempts | <80% = investigate |
| **Transcript quality** | Spot-check sample (manual, weekly) | — |
| **Notification delivery rate** | Notifications sent / calls completed | <95% = investigate |
| **Avg call duration** | From call records | Track trend, no hard alert |

### Implementation:
```sql
-- Call quality view per tenant (Supabase)
CREATE VIEW call_quality_metrics AS
SELECT
  org_id,
  DATE_TRUNC('day', created_at) AS day,
  COUNT(*) AS total_calls,
  AVG(first_response_ms) AS avg_first_response,
  AVG(turn_latency_ms) AS avg_turn_latency,
  SUM(CASE WHEN status = 'error' THEN 1 ELSE 0 END)::FLOAT / COUNT(*) AS error_rate,
  SUM(CASE WHEN transfer_attempted AND transfer_succeeded THEN 1 ELSE 0 END)::FLOAT /
    NULLIF(SUM(CASE WHEN transfer_attempted THEN 1 ELSE 0 END), 0) AS transfer_success_rate
FROM calls
GROUP BY org_id, DATE_TRUNC('day', created_at);
```

---

## 5. Incident Runbook

### Auto-created file template

```markdown
# Incident: [auto-generated summary]

**Detected:** YYYY-MM-DD HH:MM EST
**Severity:** P0/P1
**Status:** Investigating / Mitigated / Resolved
**Duration:** [filled after resolution]

## Timeline
- HH:MM — Alert triggered: [detail]
- HH:MM — Eugene acknowledged
- HH:MM — Root cause identified: [detail]
- HH:MM — Fix deployed
- HH:MM — Confirmed resolved

## Root cause
[Filled after resolution]

## Impact
- Customers affected: [count]
- Calls missed: [estimate]
- Duration: [minutes]

## Prevention
[What changes prevent recurrence]

## Action items
- [ ] [Specific follow-up]
```

### Location: `docs/incidents/YYYY-MM-DD-summary.md`

---

## 6. Structured Logging

**Library:** Pino (fast JSON logger)

**Log levels:**
| Level | When |
|-------|------|
| `error` | Unexpected failure, needs attention |
| `warn` | Recoverable issue, may need attention |
| `info` | Normal operation events (call started, call ended, notification sent) |
| `debug` | Detailed diagnostic (disabled in production) |

**Required fields in every log entry:**
```json
{
  "level": "info",
  "time": "2026-04-18T20:00:00Z",
  "msg": "Call completed",
  "org_id": "org_123",
  "call_id": "call_456",
  "duration_ms": 145000,
  "transfer_attempted": false
}
```

**PII rules:**
- Phone numbers: last 4 only (`***-***-1234`)
- Names: NEVER logged
- Transcripts: NEVER logged (stored in DB with RLS)
- API keys: NEVER logged

**Retention:** 30 days on server, then rotated (pm2-logrotate or equivalent).

---

## Cost Projection

| Customers | Sentry | Upptime | PostHog | Synthetic calls | Total |
|-----------|--------|---------|---------|----------------|-------|
| 0-10 | $0 | $0 | $0 | $5 | **$5** |
| 10-50 | $0 | $0 | $0 | $5 | **$5** |
| 50-100 | $26 (Team) | $0 | $0 | $5 | **$31** |
| 100+ | $26 | $0 | $350+ | $5 | **$381** |

Free tiers cover us until ~50 customers. At that point, revenue ($3,950+/mo) easily covers observability costs.
