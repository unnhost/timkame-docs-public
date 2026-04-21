# Disaster Recovery — TimkaMe v6

> Phase H.12 deliverable. Compiled 04/18/2026.

---

## Backup Strategy

### Database (Supabase)
- **PITR (Point-in-Time Recovery):** enabled, continuous WAL archiving
- **RPO target:** <1 hour (Supabase PITR typically <5 min lag)
- **Retention:** 7 days of PITR history
- **Manual snapshot:** weekly, stored in Supabase (automatic)

### Application code
- **Git:** GitHub is the source of truth. All code in version control.
- **Secrets:** Doppler/Infisical. Separate from code, separate backup.
- **Content blocks:** in Git (`/content/data/`), versioned with code.

### External dependencies (not backed up by us)
- Telnyx: call recordings stored 30 days on Telnyx, then copied to our storage
- Stripe: billing data lives in Stripe, accessible via API
- Telegram: notification history not backed up (ephemeral)

## Recovery Targets

| Scenario | RTO (time to recover) | RPO (data loss) |
|----------|----------------------|-----------------|
| Database corruption | <4 hours | <1 hour |
| Server failure | <2 hours | Zero (code in Git) |
| Secrets leaked | <1 hour (rotate all) | N/A |
| DNS failure | <1 hour (Cloudflare) | N/A |
| Full data loss (worst case) | <8 hours | <1 hour |

## Quarterly Restore Drill

**Frequency:** Every 3 months.
**Environment:** Staging (never production).
**Duration:** ~2 hours.

### Drill procedure

1. **Simulate:** "Database corruption detected at [timestamp]"
2. **Restore:** Use Supabase PITR to restore to point before "corruption"
   ```
   # Supabase dashboard → Database → Point-in-time Recovery
   # Select timestamp 5 minutes before simulated corruption
   # Initiate restore to staging project
   ```
3. **Verify data integrity:**
   - [ ] Organizations table has expected row count
   - [ ] Recent calls (last 24h before restore point) present
   - [ ] Transcripts readable
   - [ ] RLS policies active (test cross-tenant isolation)
   - [ ] Auth users can log in
4. **Measure:**
   - Actual RTO: ___ minutes (target: <240)
   - Actual RPO: ___ minutes (target: <60)
5. **Document:** results in `docs/incidents/drill-YYYY-MM-DD.md`

### Drill checklist
- [ ] Supabase PITR is actually enabled (verify, don't assume)
- [ ] Restore to staging successful
- [ ] Data integrity verified
- [ ] RTO within target
- [ ] RPO within target
- [ ] Eugene practiced the restore process (not just Claude)

## Incident Categories

### Database corruption
1. Stop writes (maintenance mode)
2. Identify last known good timestamp
3. Restore via PITR
4. Verify data
5. Resume service
6. Status page update
7. Postmortem

### Server failure (hosting goes down)
1. Verify outage (not just monitoring false alarm)
2. If hosting provider outage → wait + status page update
3. If our server → restart/redeploy from Git
4. `git clone` + `npm install` + `npm run build` + secrets from Doppler
5. DNS already pointing to correct IP (Cloudflare, fast propagation)

### Secrets compromised
1. Identify scope (which secrets exposed)
2. Rotate ALL potentially affected secrets immediately:
   - Telnyx API key
   - Stripe API keys (restricted + secret)
   - Supabase service role key
   - OpenAI API key
   - Doppler access tokens
3. Update in secrets manager
4. Redeploy all services
5. Audit logs for unauthorized access
6. If customer data affected → NY SHIELD Act: notify within 72 hours

### DNS / Cloudflare failure
1. Verify Cloudflare status
2. If Cloudflare down → direct DNS possible? (unlikely, accept downtime)
3. If our config → fix in Cloudflare dashboard
4. Status page (if accessible) updated

## Data Retention (per Privacy Policy)

| Data | Retention | Deletion method |
|------|-----------|----------------|
| Call recordings | 90 days | Automated DB job |
| Call transcripts | 90 days | Automated DB job |
| Consent records | 5 years | Manual (legal requirement) |
| Account data (active) | While active | N/A |
| Account data (terminated) | 30 days after termination | Automated job |
| Audit logs | 1 year | Automated job |
| Stripe billing data | Per Stripe retention | Stripe manages |
