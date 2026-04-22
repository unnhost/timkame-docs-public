# Technical Debt — TimkaMe v6

Justified shortcuts taken during development. Each entry has: what/why/cost/cleanup timing.

Format:
```
### TD-NNN: [Short title]
**What:** [The shortcut taken]
**Why:** [Why it was acceptable now]
**Cost:** [What breaks or degrades if not cleaned up]
**Cleanup by:** [Date or phase]
```

---

### TD-001: Google Analytics integration for landing pages
**What:** All vertical landing pages need GA tracking. v5 had GA tag (G-7G04E5DXFD) but needs audit for v6 domain.
**Why:** Launching without analytics is acceptable for first demo — need traffic before analytics matter.
**Cost:** No visibility into landing page performance, conversion rates, traffic sources.
**Cleanup by:** P2 — after auth fix and first customer demo.

### TD-002: SEO audit and optimization of landing pages
**What:** Meta tags, Open Graph images, canonical URLs, sitemap.xml, robots.txt all reference v5 domain/pricing. Schema.org markup outdated.
**Why:** SEO takes weeks to index anyway. First priority is direct demos to warm leads, not organic traffic.
**Cost:** Poor search ranking, broken social previews when sharing URLs.
**Cleanup by:** P2 — after auth fix and first customer demo.

### TD-003: @ts-nocheck in v5 ported components
**What:** 30+ v5 components have @ts-nocheck because they reference v5 Supabase schema (businesses vs organizations, business_id vs org_id).
**Why:** Full schema migration across 30+ files would delay the port by days. Core pages (dashboard, calls, settings) are properly typed.
**Cost:** Type errors hidden in analytics, billing, notifications, onboarding pages. Runtime errors possible.
**Cleanup by:** Phase 2 — as each page is activated for real use.

### TD-004: Logout button in sidebar non-functional
**What:** Logout button in DashboardLayout doesn't work. Navigates to /logout page but inline onClick handler never fires due to broken v5 JS on the page.
**Why:** v5 components (NotificationsDropdown, TrialBanner, UsageBanner) were disabled but residual JS errors on dashboard prevent event handlers from working reliably.
**Cost:** Users must manually navigate to /logout. UX friction.
**Cleanup by:** Next session — trace remaining JS errors in DashboardLayout, remove all v5 component stubs.

### TD-005: Cloudflare Turnstile not enforced on signup
**What:** Turnstile CAPTCHA is skipped when NEXT_PUBLIC_TURNSTILE_SITE_KEY is not set. Currently not set in Doppler.
**Why:** Eugene needs to create a Turnstile site for v6.timkame.com in Cloudflare dashboard first.
**Cost:** Signup form vulnerable to bot abuse. Acceptable for MVP with manual user creation.
**Cleanup by:** Before public launch — Eugene creates Turnstile site, adds key to Doppler.

### TD-006: v5 @ts-nocheck components break dashboard JS
**What:** NotificationProvider, NotificationsDropdown, TrialBanner, UsageBanner replaced with stubs, but other v5 components still on page may call v5 APIs or reference v5 schema.
**Why:** Full component-by-component audit and migration is multi-day effort. Core pages work.
**Cost:** Dashboard JS may be unreliable — some interactive elements (like logout button) don't respond.
**Cleanup by:** Next session — systematic audit of all client components on dashboard layout.
