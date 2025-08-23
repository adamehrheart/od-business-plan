# Open Dealer Roadmap

Status: Phase 1 (Pilot) – RSM Honda in progress

## 1) Executive Overview
Objective: Enable local dealerships to make inventory highly visible to LLMs via validated Schema.org feeds and APIs, centralized in a searchable marketplace.

Domains
- Primary: https://opendealer.app
- Alternates: https://opendealer.pro, https://opendealer.ai

Related docs
- Business overview: README.md
- Architecture: architecture/overview.md
- Market analysis: business/market-analysis.md
- API reference: technical/api-reference.md

---
## 2) Quickstart & Priorities (Weeks 1–4)
Minimal set to start
- od-schema: Vehicle type, validation, JSON-LD builder (planned)
- od-soap-transformer: HomeNet SOAP → normalized Vehicle[] (current)
- od-db: Supabase schema + RLS (current)
- od-data-api: POST /v1/vehicles/batch (current), reads/planned

Immediate priorities
| Task | Status |
|------|--------|
| Build SOAP transformer (serverless + zod validation) | In progress |
| Set up Supabase schema + RLS | Complete |
| Publish schema package v0.1 | Future (Phase 2) |
| Implement Data API batch POST | Complete |
| Implement reads/feeds (JSON-LD, sitemaps) | Future (Phase 2/3) |

---
## 3) System Overview

Ingestion
- Primary: Apify (SaaS scraping) for JS-heavy sites
- API: Serverless function for SOAP→JSON transformation
- Validation: Zod Schema (VehicleV1) before storage

Storage & Management
- Supabase (Postgres) single source of truth
- Retention: purge stale listings
- CMS (Payload) for onboarding, UTM config, slugs, job scheduling

API Layer
- Current: REST – POST /v1/vehicles/batch
- Planned: Reads – GET /api/v1/vehicles (Phase 2)
- Planned: JSON-LD feeds per dealer (Phase 2)
- Planned: Sitemaps per dealer (Phase 3)
- Security: API key + dealer slug; RLS

Dealer Tools (Future)
- Embeddable JSON-LD snippet with wrapped links (Phase 3)

Marketplace (Future)
- Public searchable listings with Schema.org markup (Phase 3+)

Redirect Service (Future)
- Versioned link wrapper: https://links.opendealer.app/v1/{service}/{dealer_id}/{vin}
- Click event logging in Supabase; metrics for dealers (Phase 2/3)

---
## 4) Dev Conventions & Governance
- Repos prefixed `od-`; env vars prefix `OD_`
- Env: secrets only (tokens, service keys)
- DB: multi-tenant config (dealer slug, domain, platform hints)
- Phases for config: env → DB → CMS
- Logging: include reqId/dealerSlug; exclude secrets/PII
- Keys scoped per dealer; rotation plan via CMS (Phase 2)

---
## 5) Phases & Milestones

Phase 1 (Pilot: single dealer)
- SOAP transformer, Apify scraper
- Supabase schema + RLS
- Data API POST /v1/vehicles/batch
- Scheduler (Vercel Cron) for ingestion
- Merge logic (composite key vin+dealerslug)
- Rebrandly integration stub (optional cache table)

Phase 2 (Reads & Feeds)
- GET /api/v1/vehicles (scoped reads)
- JSON-LD per vehicle/dealer
- Key rotation via CMS
- Rate limiting & usage metrics
- Publish `od-schema` package
 - Rebrandly custom domain and short-link get-or-create utility

Phase 3 (Discovery & Dealer Tools)
- Sitemaps per dealer
- Redirect service + metrics
- Embeddable JSON-LD snippet
- Marketplace (public discovery)
 - Dealer analytics dashboard (clicks, completeness, freshness)

Phase 4 (Scale)
- Multi-dealer onboarding flow
- Partnerships (Google Vehicle Listings, Bing)
- Advanced analytics and dashboards
- Internationalization

---
## 6) Reference Spec (Condensed)

Services
- od-soap-transformer: serverless SOAP→JSON
- od-scrapers: Apify actors; POST /v1/vehicles/batch
- od-schema: shared zod types, normalizers, JSON-LD builder (planned)
- od-data-api: validate + write; future reads/feeds
- od-db: SQL migrations, RLS, indexes
- od-cms: onboarding, UTM config, job scheduling
- od-docs: dev/dealer docs (private)

Redirect events table (planned)
```sql
CREATE TABLE redirect_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  dealer_id UUID NOT NULL,
  vin TEXT,
  target_url TEXT NOT NULL,
  referrer TEXT,
  user_agent TEXT,
  ip INET,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX ON redirect_events (dealer_id);
CREATE INDEX ON redirect_events (vin);
```

---
## 7) Open Risks & Mitigations
- Source variability (scrapers): use Apify; CSS selectors stored per platform in CMS
- Auth/config sprawl: centralize env management; move to CMS (Phase 2)
- Data quality: canonical schema + merge logic by (vin, dealerslug)
- Rate limiting: enforce per dealer (Phase 2)

---
## 8) Appendix
- Sources: see business/market-analysis.md (NADA, OpenAI DevDay, etc.)
- Strategic notes from ChatGPT briefing integrated into phases above
