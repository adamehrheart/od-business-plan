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
- Rebrandly-managed short links + metrics (no custom redirect layer)
- Embeddable JSON-LD snippet
- Marketplace (public discovery)
- Dealer analytics dashboard (clicks, completeness, freshness)

Phase 4 (Scale)
- Multi-dealer onboarding flow
- Partnerships (Google Vehicle Listings, Bing)
- Advanced analytics and dashboards
- Internationalization

---
## 6) Detailed Phase Checklists

Legend: [x] Completed  [ ] Planned  [~] In progress / partial

### Phase 1 – Pilot (Single Dealer)
- [x] Supabase schema + RLS (vehicles table, indexes, policies)
- [x] Data API: POST `/v1/vehicles/batch` with validation and merge-upsert by `(vin, dealerslug)`
- [x] SOAP transformer deployed (HomeNet → normalized Vehicle[])
- [x] Scheduler: Vercel Cron job triggers ingestion
- [x] Authentication hardened (header checks, trimming, case-insensitive)
- [x] Apify scrapers posting batches to Data API
- [x] CMS: dealer slugs, UTM config, scheduled job management
- [x] Deterministic `dealer_id` from `dealerslug` (UUIDv5)
- [~] Rebrandly integration: columns + utility module added; end-to-end wiring pending

Definition of Done (Phase 1)
- Data reliably ingested for pilot dealer(s), persisted, deduplicated, and merge-upserted
- Operational visibility via logs and job history; retries on transient failures

### Phase 2 – Reads & Feeds
- [ ] GET `/api/v1/vehicles` (scoped reads; filters + pagination)
- [ ] JSON-LD per vehicle/dealer endpoints
- [ ] Rate limiting and usage metrics per dealer
- [ ] API key rotation via CMS (issue/disable/rotate)
- [ ] Publish `od-schema` package for shared types and validators
- [ ] Rebrandly custom domain + get-or-create short-link utility wired into outputs
 - [ ] Centralized robots/sitemaps for stack with AI bot allowlist
 - [ ] IndexNow pings on inventory changes

Definition of Done (Phase 2)
- LLM/consumer-friendly read APIs and JSON-LD feeds available and documented
- Observability and quotas in place; keys lifecycle managed via CMS

### Phase 3 – Discovery & Dealer Tools
- [ ] Sitemaps per dealer (XML + JSON variants)
- [ ] Embeddable JSON-LD snippet for dealer sites
  - [ ] Snippet generator per dealer (includes canonical URL + UTM)
  - [ ] Security: integrity attributes, CSP guidance, minimal surface
  - [ ] Platform guides (Dealer.com, VinSolutions, generic HTML)
  - [ ] Versioning & rollback strategy
- [ ] Marketplace (public discovery)
  - [ ] Index page with search (make/model/price/mileage)
  - [ ] Dealer landing pages with inventory counts
  - [ ] Per‑vehicle page with JSON‑LD and canonical dealer URL
  - [ ] SEO: titles, meta, OpenGraph, structured data validation
  - [ ] Performance: ISR, edge cache, image optimization
  - [ ] Accessibility and mobile UX
  - [ ] Robots/canonicals to avoid SEO duplication with dealer sites
- [ ] Dealer analytics dashboard
  - [ ] Clicks (Rebrandly), completeness %, freshness (updated_at)
  - [ ] Ingestion health, error rates, retry counts
  - [ ] Top performing listings and sources
- [ ] Rebrandly‑managed short links for marketplace CTAs; JSON‑LD uses canonical URLs

Definition of Done (Phase 3)
- Dealers gain discovery and analytics out-of-the-box; marketplace live

### Phase 4 – Scale & Self-Service
- [ ] Multi-dealer onboarding flow (self-service)
- [ ] Platform detection engine (Dealer.com, HomeNet, VinSolutions)
- [ ] Automated scraper deployment via Apify templates
- [ ] Zero-code implementation via platform APIs (script injection + verification)
- [ ] Partnerships (Google Vehicle Listings, Bing); internationalization
 - [ ] Docs page for developers (feeds, update frequency, schema)

Definition of Done (Phase 4)
- Dealers self-serve from signup to activation; partnerships and intl ready

Notes
- Deep detail per track: see Section 8 and `references/ProjectPlan.md`

---
## 7) Reference Spec (Condensed)

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
## 8) Open Risks & Mitigations
- Source variability (scrapers): use Apify; CSS selectors stored per platform in CMS
- Auth/config sprawl: centralize env management; move to CMS (Phase 2)
- Data quality: canonical schema + merge logic by (vin, dealerslug)
- Rate limiting: enforce per dealer (Phase 2)

---
## 9) Deep‑Dive Tracks (from prior plan)

These tracks retain the detailed design from the previous `ProjectPlan.md`. See `references/ProjectPlan.md` for full write‑ups.

1. Self‑Service Onboarding (Phase 3–4)
   - Platform Detection Engine (Dealer.com, HomeNet, VinSolutions)
   - Credential collection & validation (OAuth where supported)
   - Automated scraper deployment via Apify templates
   - Snippet generation, payment (Stripe), activation, monitoring

2. Zero‑Code Implementation (Phase 3–4)
   - Platform API script injection, verification, fallback strategies
   - Performance monitoring, update management, error recovery

3. Dealer Dashboard (Phase 3–4)
   - Ingestion health, data quality/completeness, analytics, billing
   - Support workflow and documentation integration

4. Link Management & Analytics (Phase 2–3)
   - Rebrandly custom domain (`links.opendealer.app`) get‑or‑create utility
   - Optional cache table for short‑link mappings; click metrics via Rebrandly API
   - JSON‑LD uses canonical dealer URLs; marketplace CTAs use short links

5. Reads & Feeds (Phase 2)
   - Scoped `GET /api/v1/vehicles`, JSON‑LD endpoints, rate limiting, usage metrics

6. Infra, Monitoring & Ops (Phase 2–4)
   - Env/secrets management, scheduled jobs, alerting, backups
   - RLS alignment with API keys and per‑dealer scoping

References: `references/ProjectPlan.md`

---
## 10) Appendix
- Sources: see business/market-analysis.md (NADA, OpenAI DevDay, etc.)
- Strategic notes from ChatGPT briefing integrated into phases above

## Phase: Async Pipeline & Distribution (Next 4–8 weeks)
- Implement DB-backed queues (`job_queue`) with idempotency and backoff
- Add `vehicle_links` cache table and url_shortening worker
- Add geocoding worker to populate dealer `location` (lat/lng)
- Add IndexNow pinger job on create/update/sold
- Enforce source isolation in ingestion; reject mixed-source batches
- Implement nearby search (lat,lng,radius) + distance in responses
- Update JSON-LD to include `offeredBy` PostalAddress + GeoCoordinates
- Configure robots: stack allows GPTBot/Claude/Perplexity; dealer site for Google/Bing
- Publish sitemaps per object type with `lastmod`; strong ETag/Last-Modified on feeds

## Phase: Scale & Partners (Following 4–8 weeks)
- Rate-limit aware batching for Rebrandly and geocoding
- Introduce DLQ and operational dashboards (queue depth/age, success rates)
- Prepare Google Vehicle Listings partner feed for Gemini placement
- Evaluate migration from DB queue to managed queue (SQS/Pub/Sub) if needed
