# Decisions Log — TimkaMe v6

Template for new entries:
```
### DEC-NNN: [Short title]
**Date:** MM/DD/YYYY
**Context:** [2 lines max]
**Options:** [List]
**Chosen:** [Option]
**Rationale:** [2-3 lines]
**Revisit by:** [Date or N/A]
```

---

### DEC-001: Telnyx over Twilio for telephony
**Date:** 04/18/2026
**Context:** Need managed telephony with per-customer isolation. Twilio is current stack but expensive and complex.
**Options:** (a) Twilio subaccounts, (b) Telnyx managed accounts, (c) Twilio Connect BYOC
**Chosen:** (b) Telnyx managed accounts
**Rationale:** ~50% cheaper voice ($0.0055 vs $0.0085 inbound), 10DLC bulk API 7x faster, free 24/7 support (Twilio $250+/mo), native OpenAI Realtime integration. Locked decision from v6 plan.
**Revisit by:** N/A (locked)

### DEC-002: Single pricing tier at $79/mo
**Date:** 04/18/2026
**Context:** Previous codebase had 4 tiers ($0/49/79/199). Complexity without users to justify it.
**Options:** (a) Keep 4 tiers, (b) Single $79 tier, (c) Single $49 tier
**Chosen:** (b) Single $79/mo, 500 min, $0.15/min overage, 14-day free trial no CC
**Rationale:** Zero users = no legacy to protect. Single tier simplifies billing, onboarding, and content. No Business tier until 10+ paying customers exist. $79 at 500 min is competitive (Rosie $49/250min, My AI Front Desk $99/200min).
**Revisit by:** After 10 paying customers

### DEC-003: Message-taker positioning, no HIPAA
**Date:** 04/18/2026
**Context:** Dental practices are a target vertical but HIPAA compliance is expensive and complex for a solo founder.
**Options:** (a) Offer HIPAA BAA tier, (b) Position as message-taker only, no PHI
**Chosen:** (b) Message-taker — AI collects name, callback number, reason for call. Never DOB, SSN, medical info.
**Rationale:** BAA requires compliant infra across Telnyx+Supabase+OpenAI, $1k+/mo. Solo founder can't maintain. PHI exclusion hard-enforced in prompt editor via regex + LLM validation.
**Revisit by:** N/A until customer demand demonstrates otherwise

### DEC-004: Telegram-first notifications
**Date:** 04/18/2026
**Context:** Russian SMB owners in NY/NJ use Telegram daily, rarely read business email.
**Options:** (a) Email-first, (b) Telegram-first, (c) SMS-first
**Chosen:** (b) Telegram-first, email as fallback only. WhatsApp in Phase 3+ if requested.
**Rationale:** Target market behavior. Telegram is standard communication in Russian-immigrant business community. Email gets buried. SMS costs money per message.
**Revisit by:** Phase 3 based on customer feedback

### DEC-006: Telnyx PAYG (pay-as-you-go) — RESOLVED
**Date:** 04/18/2026 (resolved 04/18/2026)
**Context:** Telnyx Managed Accounts require $1,000/mo minimum commit. At 0-50 customers, actual usage ~$20-50/mo.
**Options:**
(a) Telnyx pay-as-you-go, app-layer tenant isolation via Supabase RLS
(b) Negotiate startup waiver
(c) Twilio subaccounts
(d) Hybrid PAYG + managed later
**Chosen:** (a) Telnyx PAYG. Single master account, tenant isolation at application layer (Supabase RLS). Upgrade to managed accounts when monthly Telnyx usage naturally approaches $1K (~150+ customers).
**Rationale:** $1K/mo minimum breaks $100/mo budget at early stage. PAYG usage estimate $20-50/mo. No per-customer Telnyx subaccount isolation needed — RLS handles it. Eugene approved 04/18/2026.
**Revisit by:** When Telnyx monthly bill approaches $800

### DEC-007: OpenAI Realtime + Telnyx SIP = no middleware — RESOLVED
**Date:** 04/18/2026 (resolved 04/18/2026)
**Context:** Telnyx supports direct SIP routing to OpenAI Realtime API endpoint. Eliminates Pipecat/WebSocket middleware.
**Options:** (a) Direct Telnyx SIP → OpenAI, (b) Keep Pipecat middleware, (c) Hybrid
**Chosen:** (a) Direct Telnyx SIP → OpenAI Realtime. No Pipecat. No middleware.
**Rationale:** A.4 voice gate PASSED — OpenAI Realtime Russian quality confirmed by Eugene. Direct SIP = simplest stack, lowest latency (~450ms), eliminates ~800 lines of middleware code. Locked to OpenAI for voice, acceptable trade-off.
**Revisit by:** Only if OpenAI degrades Russian quality or pricing becomes prohibitive

### DEC-010: Voice stack = OpenAI Realtime direct SIP
**Date:** 04/18/2026
**Context:** A.4 voice quality gate required blind listening test of Russian voice. Eugene tested OpenAI Realtime and confirmed quality is good.
**Options:** (a) OpenAI Realtime via SIP, (b) Hybrid (Deepgram STT + GPT-4.1 + ElevenLabs TTS), (c) Wait 3 months
**Chosen:** (a) OpenAI Realtime via direct Telnyx SIP routing
**Rationale:** Russian quality verified by founder. Simplest stack — SIP trunk points at `sip:$PROJECT_ID@sip.api.openai.com`, OpenAI handles STT+LLM+TTS in one service. ~450ms time-to-first-voice. No middleware server to maintain.
**Revisit by:** N/A (locked, unless OpenAI Russian quality degrades)

### DEC-008: Landing page default language — English
**Date:** 04/18/2026
**Context:** Eugene's explicit direction. Russian-speaking SMBs are the target, but landing should be English-first with prominent RU toggle.
**Options:** (a) Russian default with EN toggle, (b) English default with RU toggle, (c) Browser auto-detect
**Chosen:** (b) English default. Prominent "RU / Русский" button in header. Russian landing at /ru is separately authored, not i18n translation.
**Rationale:** EN catches bilingual/English-first searchers and partners. RU landing is hand-crafted for Russian-speaking Brooklyn SMB mentality — different tone, trust signals, FAQ. No auto-redirect.
**Revisit by:** N/A (locked)

### DEC-009: LLC filing deferred to pre-launch sequence
**Date:** 04/18/2026
**Context:** LLC was Phase 0 in earlier plan versions. Eugene's edit: only required before first paying customer, not before building.
**Options:** (a) LLC before any coding, (b) LLC when demo-ready + 3 warm prospects, (c) LLC only when first customer pays
**Chosen:** (b) LLC when product is demo-ready AND 3+ warm prospects verbally committed. Trigger = Phase 0.5.
**Rationale:** LLC costs money and takes 6 weeks (Albany publication). No reason to start the clock before the product exists. Eugene operates as sole proprietor with EIN during development. No "TimkaMe LLC" branding, no paid contracts, no live Stripe until LLC complete.
**Revisit by:** N/A (locked)

### DEC-011: RLS helper function in public schema, not auth schema
**Date:** 04/19/2026
**Context:** Supabase pooler connection (Supavisor) does not allow creating functions in the `auth` schema. The target-architecture.md implied placing the `user_org_id()` helper in `auth` schema alongside `auth.uid()`.
**Options:** (a) Place in `auth` schema (requires direct DB connection), (b) Place in `public` schema with SECURITY DEFINER
**Chosen:** (b) `public.user_org_id()` with `SECURITY DEFINER` and `SET search_path = public`
**Rationale:** Pooler-compatible, works with `supabase db push --db-url`. SECURITY DEFINER ensures the function runs with owner privileges (can read `user_profiles` even when RLS is enabled). `SET search_path` prevents search_path injection.
**Revisit by:** N/A

### DEC-012: Supabase DB connection via pooler (aws-1-us-west-2)
**Date:** 04/19/2026
**Context:** No Supabase Management API access token in Doppler, so `supabase link` fails. Direct DB hostname (`db.PROJECT_REF.supabase.co`) is IPv6-only and doesn't resolve on this network.
**Options:** (a) Generate Management API token, (b) Use `supabase db push --db-url` via session-mode pooler
**Chosen:** (b) Push via `postgresql://postgres.PROJECT_REF:PASSWORD@aws-1-us-west-2.pooler.supabase.com:5432/postgres?sslmode=require`
**Rationale:** Works without extra credentials. Port 5432 = session mode (needed for migrations/DDL). Transaction mode (6543) doesn't support DDL.
**Revisit by:** When Supabase Management API token is added to Doppler

### DEC-005: v6 skills architecture — 6 consolidated skills
**Date:** 04/18/2026
**Context:** Previous project had 10+ skills, too many to hold consistently across sessions.
**Options:** (a) Keep existing 10+ skills, (b) Consolidate to 6 broader skills, (c) No skills
**Chosen:** (b) 6 skills: project-memory, decision-logger, code-quality, content-standards, security-defaults, completion-discipline
**Rationale:** Fewer broader skills with clear scope beat many narrow ones. Organized for less cognitive friction. Each covers the surface area of ~2 original skills.
**Revisit by:** After 4 weeks of use — check docs/skill-feedback.md

### DEC-013: Auth flow — Supabase SDK on both client and server
**Date:** 04/19/2026
**Context:** Need auth for both Next.js frontend and Hono API backend. User specified @supabase/ssr for Next.js and auth routes on Hono.
**Options:** (a) Auth only via Next.js @supabase/ssr, (b) Auth only via Hono, (c) Both — Next.js for cookie sessions, Hono for API token auth
**Chosen:** (c) Dual approach. Next.js uses @supabase/ssr for cookie-based sessions (signup/login pages, middleware). Hono validates JWT from Authorization header for API calls.
**Rationale:** Frontend needs cookie-based sessions for SSR. Backend API needs token auth for mobile clients and service-to-service calls. Both share the same Supabase Auth instance.
**Revisit by:** N/A

### DEC-014: RLS uses user_org_id() function, not JWT custom claims
**Date:** 04/19/2026
**Context:** Task specified JWT custom claim `org_id` via Custom Access Token Hook. Existing migrations already use `user_org_id()` SECURITY DEFINER function.
**Options:** (a) JWT custom claim via auth hook, (b) user_org_id() function with subquery
**Chosen:** (b) Keep user_org_id() function approach. Already implemented and tested (14/14 isolation tests pass).
**Rationale:** JWT claims require configuring auth hooks in Supabase dashboard, adding complexity. The function approach is simpler, works now, and performance is adequate for current scale (indexed lookup on user_profiles.id).
**Revisit by:** When query latency becomes a concern (100+ concurrent users)

### DEC-015: Generated Supabase types for type-safe queries
**Date:** 04/19/2026
**Context:** Supabase JS client returns `never` types without database type hints, causing TypeScript errors.
**Options:** (a) Manual type assertions, (b) Generate types from remote schema
**Chosen:** (b) `supabase gen types typescript --linked` → `packages/db/src/database.types.ts`. Auto-generated, 639 lines (exempt from 400-line rule).
**Rationale:** Type safety across all DB queries. Regenerate after schema changes.
**Revisit by:** N/A

### DEC-016: Telnyx TeXML + OpenAI SIP dual-webhook architecture
**Date:** 04/19/2026
**Context:** Need to route inbound calls from Telnyx to OpenAI Realtime via SIP. Two approaches: direct SIP forwarding or TeXML intermediary.
**Options:** (a) Direct Telnyx SIP trunk → OpenAI, (b) TeXML Dial → OpenAI SIP, (c) Call Control API bridge
**Chosen:** (b) TeXML `<Dial><Sip>` with dual webhooks. Telnyx webhook creates call record and returns TeXML. OpenAI webhook accepts call with AI config.
**Rationale:** TeXML gives us Telnyx-side call lifecycle events (status callbacks for duration/hangup). Direct SIP would skip Telnyx events entirely. TeXML is simpler than Call Control API.
**Revisit by:** N/A

### DEC-018: DNS-only (grey cloud) + Let's Encrypt for v6.timkame.com
**Date:** 04/19/2026
**Context:** v6 serves voice webhooks from Telnyx and OpenAI Realtime WebSocket. Cloudflare proxy can interfere with SIP/WebSocket handshakes and add latency to real-time voice paths.
**Options:** (a) Cloudflare proxy (orange cloud) + origin cert, (b) DNS-only (grey cloud) + Let's Encrypt
**Chosen:** (b) DNS-only + Let's Encrypt certbot on nginx. Cloudflare DNS record for `v6.timkame.com` set to grey cloud (DNS only, no proxy).
**Rationale:** Voice webhooks (Telnyx call events, OpenAI Realtime WebSocket) work more reliably without Cloudflare proxy in between. Let's Encrypt is free, certbot auto-renews. Direct connection = lower latency for real-time voice. Eugene's explicit decision.
**Revisit by:** N/A

### DEC-017: Cloudflare Tunnel for dev webhook testing
**Date:** 04/19/2026
**Context:** Both Telnyx and OpenAI need to reach our local dev server via HTTPS for webhooks.
**Options:** (a) Cloudflare Tunnel (free), (b) ngrok (freemium), (c) Deploy to server first
**Chosen:** (a) Cloudflare Tunnel — `cloudflared tunnel --url http://localhost:5050`. Free, reliable, no signup needed.
**Rationale:** No account required, temporary URL is fine for dev. Script `setup-telnyx.ts` takes the tunnel URL as arg.
**Revisit by:** N/A

### DEC-018: Voice quality — coral + whisper-1 + server_vad
**Date:** 04/20/2026
**Context:** Marin voice + semantic_vad + gpt-4o-transcribe had quality issues on phone audio.
**Options:** (a) Keep current config, (b) coral + whisper-1 + server_vad + pcmu
**Chosen:** (b) coral voice, whisper-1 transcription, server_vad (threshold:0.8, silence:700ms, prefix:500ms), explicit pcmu audio, far_field noise reduction
**Rationale:** Coral is better for multilingual. Whisper-1 is proven for Russian phone audio. server_vad is more predictable than semantic_vad for telephony. far_field is safe with server_vad.
**Revisit by:** After 50 test calls

### DEC-019: Bilingual mode via language_mode column
**Date:** 04/20/2026
**Context:** US market has Russian-speaking SMBs that serve both English and Russian speakers.
**Options:** (a) Language per call detection only, (b) language_mode on phone_numbers (russian_only|english_only|bilingual)
**Chosen:** (b) language_mode column — controls system prompt, greeting, and Whisper language hint
**Rationale:** Per-number config gives business owner control. Bilingual mode uses auto-detect (no Whisper language hint) + dual-language greeting + strict "pick one and stick with it" prompt.
**Revisit by:** After customer feedback

### DEC-021: Revert PR #12 conversation flow tuning — greeting regression
**Date:** 04/20/2026
**Context:** PR #12 added interrupt_response:true + create_response:true to turn_detection. These params conflict with the explicit response.create in session.updated handler (PR #10 fix). Result: bot waited for caller to speak before greeting. Two real-call tests confirmed regression.
**Options:** (a) Fix param conflict while keeping tuning, (b) Revert PR #12 entirely
**Chosen:** (b) Full revert. PR #11 (bilingual no-Spanish fix) is the stable baseline. Voice quality tuning via session params alone is insufficient for production-grade conversation flow on direct SIP.
**Rationale:** Diminishing returns from parameter tuning without middleware. Future voice quality improvements should either (a) wait for OpenAI model improvements, or (b) introduce Pipecat/LiveKit middleware pipeline for fine-grained control over interruption, endpointing, and audio buffering. Research docs preserved in git history (commit 6427acf).
**Revisit by:** When customer feedback demands better conversation flow, or OpenAI ships improved SIP turn-taking

### DEC-022: Org-level custom prompts for AI receptionist
**Date:** 04/21/2026
**Context:** Before showing product to potential customers, demo must use their business name and context, not test data. Need org-level prompt customization.
**Options:** (a) Prompts table with versioning, (b) Columns on organizations table
**Chosen:** (b) Columns on organizations — simpler, direct, no versioning needed yet. Fields: business_description, business_hours, services_offered, services_not_offered, pricing_policy, collect_fields (JSONB), additional_instructions.
**Rationale:** YAGNI — versioned prompts are premature. Direct columns are simpler to query in webhook handler, edit in dashboard, and reason about. collect_fields as JSONB allows flexible field sets without schema changes.
**Revisit by:** When customers need prompt versioning or A/B testing

### DEC-024: Public docs mirror for Claude web session context
**Date:** 04/21/2026
**Context:** Claude web chat has stale snapshot of project files. Manual re-upload each session is friction.
**Options:** (a) Manual sync script, (b) Public mirror via GitHub Actions, (c) Stay stale
**Chosen:** (b) Public mirror at unnhost/timkame-docs-public, auto-synced from main via GitHub Actions with SSH deploy key (fully CLI-automatable, no web UI steps needed)
**Rationale:** Zero manual work after setup. Allowlist prevents leaks. Secret scan catches mistakes. SSH deploy key scoped to single repo — more secure than PAT, and can be created/rotated entirely via `gh` CLI.
**Revisit by:** If customer PII lands in docs — switch to private mirror with API access.

### DEC-025: Downgrade to Tailwind v3 for v5 frontend port
**Date:** 04/21/2026
**Context:** v5 frontend has 522 lines of globals.css + 51-line tailwind.config.js built for Tailwind v3. v6 currently has Tailwind v4 (CSS-based config). Converting v5 CSS to v4 is days of work with high regression risk.
**Options:** (a) Keep v4, convert v5 CSS, (b) Downgrade to v3, preserve v5 CSS as-is, (c) Keep both (impossible)
**Chosen:** (b) Downgrade v6 to Tailwind v3. Preserves 1 month of v5 design work. v3 is LTS and actively maintained.
**Rationale:** v5→v4 CSS conversion is out of scope and high risk. v3 works. Ship, don't polish tooling.
**Revisit by:** When Tailwind v4 ecosystem matures and migration path is clearer

### DEC-027: v5 landing served as static HTML, not converted to React
**Date:** 04/21/2026
**Context:** v5 landing is a 3,736-line standalone HTML file with inline CSS/JS/SVG animations. Three previous port attempts failed by trying to reinterpret the design in React.
**Options:** (a) Serve HTML as-is, (b) Convert to Next.js components
**Chosen:** (a) Serve v5 HTML from apps/web/public/landing/, wire / route via Next.js rewrites, use build-time template substitution for variable data from content/landing.ts
**Rationale:** Landing has no state/auth/backend integration — React adds no value. Preserves v5 design exactly. Days faster than conversion. Content blocks solved via template placeholders, not React props.
**Revisit by:** When landing needs per-visitor personalization or A/B testing

### DEC-026: Google OAuth deferred
**Date:** 04/21/2026
**Context:** v5 login had Google OAuth button. v6 port needs to decide whether to include it.
**Options:** (a) Port Google OAuth, (b) Skip — email + password + magic link only
**Chosen:** (b) Skip. Email + password + magic link via Supabase Auth only.
**Rationale:** Google OAuth requires Supabase project configuration (OAuth credentials, redirect URLs). No customer has requested it. Add when customers ask for it.
**Revisit by:** When customer requests it

### DEC-023: NEXT-SESSION.md as primary context restore file
**Date:** 04/20/2026
**Context:** Session continuity was fragile — TIMELINE, NEXT-SESSION, and MASTER-CHECKLIST all had overlapping info. Context restore loaded too many files, most of which were stale.
**Options:** (a) Keep 3-file restore, (b) Make NEXT-SESSION primary with structured sections, demote TIMELINE to debugging
**Chosen:** (b) NEXT-SESSION as primary load-bearing file. Structured: текущее состояние, задачи с оценкой, фичи в проде, известные проблемы, блокеры. TIMELINE demoted to "only if debugging". MASTER-CHECKLIST removed from restore sequence.
**Rationale:** One well-maintained file beats three stale ones. Reduces context restore from 3 reads to 1. Added session modes (короткая/глубокая) and weekly review template to prevent roadmap drift.
**Revisit by:** After 4 weeks of use
