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
