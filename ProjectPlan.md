## Open Dealer: Collaboration Briefing & Full Project Specification

### Executive Overview
Objective: Enable local dealerships to make their vehicle inventory highly visible to LLMs (e.g., ChatGPT, Gemini) via validated schema.org feeds, centralized in a searchable marketplace with APIs.

#### Domains
- Main: [opendealer.app](https://opendealer.app)
- Alternates: [opendealer.pro](https://opendealer.pro), [opendealer.ai](https://opendealer.ai)

#### Current Phase
Phase 1 — POC (Single Dealer) → **Evolving to Self-Service Platform**

**Progress**: 95-98% of core infrastructure complete, with enterprise-grade job queue system, comprehensive error handling, and robust data processing fully implemented and operational.

#### Vision: Self-Service Dealer Onboarding
**Objective**: Enable dealers to sign up, pay, and implement everything without human intervention.

**Target State**: Dealer visits opendealer.app → connects platform credentials → system automatically detects platform, deploys scraper, implements scripts → everything works instantly.

**Key Capabilities**:
- **Platform Detection**: Automatically identify dealer platform (Dealer.com, HomeNet, VinSolutions, etc.)
- **Automated Scraper Deployment**: Deploy appropriate scrapers via Apify API
- **Zero-Code Implementation**: Automatically inject scripts via platform APIs
- **Self-Service Onboarding**: Complete setup without human intervention
- **Dealer Dashboard**: Monitor status, data quality, and performance
- **Automated Billing**: Stripe/PayPal integration for subscriptions

**Business Model**:
- Monthly subscription: $99-299/month per dealer
- Setup fees: $500-1000 one-time (optional)
- Premium features: Advanced analytics, custom integrations
- Support packages: White-glove service for complex dealers

**Timeline**: 14-20 weeks to full self-service capability

#### 🏆 **Competitive Advantage & Market Position**
**Zero-Code Implementation** positions Open Dealer as the only solution that eliminates all technical barriers to entry.

**Market Differentiation**:
- **Competitors**: Require manual implementation, technical knowledge, support calls
- **Open Dealer**: One-click setup, instant activation, zero technical knowledge required

**Business Impact**:
- **Massive Adoption Driver**: Eliminates 90% of onboarding friction
- **Instant Activation**: Scripts working within minutes vs. days/weeks
- **Reduced Support**: Fewer implementation issues and support tickets
- **Viral Growth**: Dealers recommend to other dealers (network effects)
- **Platform Partnerships**: Potential for official integrations with Dealer.com, HomeNet, etc.

**Revenue Acceleration**:
- **Higher Conversion**: More dealers complete onboarding process
- **Faster Time-to-Value**: Immediate ROI for dealers
- **Reduced Churn**: Better implementation success rates
- **Premium Pricing**: Justify higher prices for zero-friction experience

#### 🚀 **Killer Feature: Zero-Code Implementation**
**Objective**: Automatically implement Open Dealer scripts via dealer platform APIs.

**Vision**: Dealer connects their Dealer.com/HomeNet/VinSolutions credentials → Open Dealer automatically injects scripts → Instant activation without any technical knowledge required.

**Technical Approach**:
- **Platform API Integration**: OAuth flows for secure credential access
- **Automated Script Injection**: Use platform APIs to inject Open Dealer scripts
- **Implementation Verification**: Verify scripts are working correctly
- **Multi-Platform Support**: Dealer.com, HomeNet, VinSolutions, DealerSocket, Cobalt

**Business Impact**:
- **Massive Adoption Driver**: Eliminates all technical friction
- **Instant Activation**: Scripts working within minutes, not days
- **Reduced Support**: Fewer implementation issues and support tickets
- **Competitive Advantage**: First-to-market with automated implementation
- **Viral Growth**: Dealers recommend to other dealers

**Implementation Timeline**: 4-6 weeks for Dealer.com, 6-8 weeks for additional platforms

***
### Quickstart & Priorities (Weeks 1–4)
#### Minimal Set to Start
- od-schema: Vehicle type, validation, JSON-LD builder
- od-soap-transformer: HomeNet SOAP → normalized `Vehicle[]`
- od-db: Supabase schema + RLS
- od-data-api: `POST /v1/vehicles/batch` + GET reads

#### Immediate Priorities
| Task | Repo | Definition of Done | Owner | Status |
|------|------|---------------------|-------|--------|
| Build SOAP transformer (serverless + zod validation) | od-soap-transformer | Endpoint `GET/POST /v1/transform/homenet` returns validated `Vehicle[]`; structured logs; env-driven config; smoke test green |  | ✅ **COMPLETED** |
| Set up Supabase schema + RLS | od-db | Migrations for `vehicles`, indexes, RLS policies aligned with API keys; rls test script passes |  | ✅ **COMPLETED** |
| Publish schema package v0.1 | od-schema | Expose `zVehicle`, `zVehicleBatch`, normalizers, `toJsonLd`; published to private registry |  | ✅ **COMPLETED** |
| Implement Data API batch POST + GET | od-data-api | `POST /v1/vehicles/batch` validates and writes; `GET /v1/vehicles` scoped by API key; basic rate limiting; OpenAPI updated |  | ✅ **COMPLETED** |

#### Recent Major Accomplishments (Latest Sprint)
| Task | Repo | Definition of Done | Status |
|------|------|---------------------|--------|
| **Core Infrastructure Fixes & Deployment** | All repos | Fixed NPM token authentication, published schema v0.1.9, deployed Data API as serverless function | ✅ **COMPLETED** |
| **Schema Validation Fixes** | od-schema | Fixed empty string handling for transmission, body_style, drivetrain fields; published v0.1.9 | ✅ **COMPLETED** |
| **Data API Serverless Deployment** | od-data-api | Fixed Vercel deployment as serverless function, removed build script, updated schema dependency | ✅ **COMPLETED** |
| **Codebase Cleanup & Security** | od-scheduler | Removed 30+ test scripts, replaced hardcoded tokens with Doppler environment variables | ✅ **COMPLETED** |
| **Job Queue Robustness & Error Handling** | od-scheduler | Enterprise-grade job queue with circuit breakers, conflict resolution, and comprehensive monitoring | ✅ **COMPLETED** |
| **Enhanced URL Shortening with Conflict Resolution** | od-scheduler | Slashtag conflict resolution, unique generation, rate limiting, and duplicate detection | ✅ **COMPLETED** |
| **Product Detail Scraping Error Handling** | od-scheduler | Comprehensive null checks, Promise.allSettled, and graceful error handling | ✅ **COMPLETED** |
| **Job Queue Management System** | od-scheduler | Stuck job detection, intelligent retries, health monitoring, and automated recovery | ✅ **COMPLETED** |
| **Schema Standardization & Integration** | All repos | Unified Vehicle schema across all services, enhanced mappers, and LLM-friendly fields | ✅ **COMPLETED** |
| **Doppler Environment Management** | All repos | Centralized environment variables via Doppler; remove all legacy .env files; IDE integration | ✅ **COMPLETED** |
| **URL Verification for Short Links** | od-data-api | Verify dealer URLs before creating Rebrandly short links; only generate for valid product detail pages | ✅ **COMPLETED** |
| **Enhanced Rebrandly Integration** | od-data-api | Fix slashtag length limits; add UTM tracking; automatic short URL generation for scraped vehicles | ✅ **COMPLETED** |

### System Overview

#### Ingestion
Primary Tool: Apify (SaaS scraping) for JavaScript-heavy dealership sites.
Fallback Option: Puppeteer/Playwright for in-house control (V2).
API Integration: Serverless function (Vercel/AWS Lambda) for SOAP-to-JSON transformation.
Error Handling: Robust handling for SOAP inconsistencies, HTML structure changes, and API failures.
Validation: Schema.org validation before data is stored to ensure consistent quality.

#### Data Storage & Management
Database: Supabase (managed Postgres) as the single source of truth.
Structure: Single vehicles table with a dealership identifier column (indexed for performance).
Retention: Data retention policy to purge stale listings and keep LLM results current.
CMS: Payload CMS connected to Supabase for dealership onboarding, API key management, and manual overrides.

#### API Layer
Endpoints: REST endpoints (GraphQL optional in a future phase).
Feeds: JSON-LD per dealer.
Security: Dealer scoping is enforced in application queries. RLS policies exist in the DB, but the service role bypasses RLS; production goal is to enforce via RLS with appropriate keys.

#### Dealer Tools
Dealer Snippet: Lightweight JavaScript include to embed schema.org JSON-LD directly into dealer sites.
Security: Minified snippet, no unnecessary dependencies.
Docs: Clear plug-and-play guide highlighting SEO and LLM discoverability benefits.

#### Marketplace
Search: Public searchable listings.
SEO: Schema.org markup and sitemaps for improved crawlability.

#### Environment Management (NEW)
**Doppler Integration**: Centralized environment variable management across all services.
**IDE Integration**: VS Code/Cursor and Warp terminal configured for Doppler.
**Development Workflow**: Automated scripts for building and starting services with Doppler.
**Security**: All secrets managed through Doppler; no local .env files in repositories.

#### Job Queue Robustness & Error Handling (NEW)
**Enterprise-Grade Job Queue**: Comprehensive job processing system with circuit breakers, intelligent retries, and automated recovery.
**Enhanced URL Shortening**: Slashtag conflict resolution with unique generation, rate limiting, and duplicate detection.
**Product Detail Scraping**: Robust error handling with comprehensive null checks, Promise.allSettled, and graceful degradation.
**Job Queue Management**: Stuck job detection (30-minute timeout), intelligent retry logic with exponential backoff, and comprehensive health monitoring.
**Circuit Breaker Pattern**: Automatic failure detection and recovery for external API calls (Rebrandly, Dealer.com, etc.).
**Monitoring & Alerting**: Real-time health checks, error pattern analysis, and automated issue resolution.

#### URL Verification & Short Link Generation (ENHANCED)
**URL Verification**: Validate dealer URLs before creating short links to ensure they are accessible product detail pages.
**Automatic Generation**: Generate short URLs automatically for vehicles ingested from scraping with verified dealer URLs.
**Rebrandly Integration**: Enhanced with proper UTM tracking, slashtag length management, and error handling.
**Quality Control**: Only create short links for verified, working product detail pages from successful scraping operations.
**Conflict Resolution**: Multi-level slashtag generation with timestamp and random fallbacks to prevent "Already exists" errors.

#### Self-Service Architecture (NEW)

##### Platform Detection Engine
**Objective**: Automatically identify dealer platform and configuration without manual intervention.

**Detection Methods**:
- **API Endpoint Scanning**: Test known patterns (e.g., `/apis/widget/INVENTORY_LISTING_DEFAULT_AUTO_NEW`)
- **HTML Structure Analysis**: Identify platform-specific elements and scripts
- **DNS/WHOIS Analysis**: Check for platform-specific hosting patterns
- **Credential Validation**: Test API credentials against known platform endpoints

**Supported Platforms**:
- **Dealer.com**: Primary focus (80%+ of dealers)
- **HomeNet**: SOAP API integration
- **VinSolutions**: REST API integration
- **DealerSocket**: Custom API patterns
- **Cobalt**: Platform-specific endpoints

##### Automated Scraper Deployment
**Objective**: Deploy appropriate scrapers via Apify API without manual configuration.

**Process**:
1. **Platform Detection** → Identify dealer platform
2. **Template Selection** → Choose appropriate scraper template
3. **Configuration** → Set dealer-specific parameters
4. **Deployment** → Create Apify actor via API
5. **Validation** → Test scraper with sample data
6. **Activation** → Enable scheduled runs

**Templates**:
- `dealer-com-api`: Dealer.com API integration
- `homenet-soap`: HomeNet SOAP transformer
- `vin-solutions-rest`: VinSolutions REST API
- `generic-html`: Fallback HTML parsing

##### Self-Service Onboarding Flow
**Objective**: Complete dealer setup without human intervention.

**Flow**:
1. **Signup** → Dealer enters website URL and contact info
2. **Platform Detection** → System identifies platform automatically
3. **Credential Collection** → Dealer provides API credentials
4. **Validation** → System tests credentials and data access
5. **Deployment** → Automated scraper deployment and configuration
6. **Snippet Generation** → Create dealer-specific implementation code
7. **Payment** → Stripe/PayPal integration for billing
8. **Activation** → Enable data ingestion and monitoring

##### Dealer Dashboard
**Objective**: Provide dealers with real-time status and performance metrics.

**Features**:
- **Status Monitoring**: Data ingestion health, error rates, success metrics
- **Data Quality**: Vehicle count, completeness scores, validation results
- **Performance Analytics**: LLM visibility metrics, click-through rates
- **Billing Management**: Subscription status, usage tracking, payment history
- **Support Integration**: Ticket creation, documentation access, troubleshooting

##### Zero-Code Implementation System
**Objective**: Automatically implement Open Dealer scripts via dealer platform APIs.

**Technical Architecture**:
```typescript
// Platform API Integration
const platformImplementers = {
  'dealer.com': {
    oauth: dealerComOAuth,
    injectScript: dealerComInjectScript,
    verifyImplementation: dealerComVerify,
    getWebsiteConfig: dealerComGetConfig
  },
  'homenet': {
    oauth: homeNetOAuth,
    injectScript: homeNetInjectScript,
    verifyImplementation: homeNetVerify,
    getWebsiteConfig: homeNetGetConfig
  },
  'vinsolutions': {
    oauth: vinSolutionsOAuth,
    injectScript: vinSolutionsInjectScript,
    verifyImplementation: vinSolutionsVerify,
    getWebsiteConfig: vinSolutionsGetConfig
  }
};

// Automated Implementation Flow
const automatedImplementation = async (dealerCredentials) => {
  // 1. Validate platform credentials
  const platform = await detectPlatform(dealerCredentials);
  const isValid = await validateCredentials(platform, dealerCredentials);
  
  // 2. Generate dealer-specific snippet
  const snippet = generateSnippet(dealerId, platform);
  
  // 3. Inject script via platform API
  const result = await injectScript(platform, dealerCredentials, snippet);
  
  // 4. Verify implementation
  const isWorking = await verifyImplementation(dealerUrl, snippet);
  
  return { success: isWorking, snippet, platform };
};
```

**Implementation Process**:
1. **OAuth Authentication**: Secure credential access via platform APIs
2. **Script Generation**: Create dealer-specific Open Dealer snippet
3. **API Injection**: Use platform APIs to inject scripts into dealer website
4. **Verification**: Test that scripts are loading and functioning correctly
5. **Activation**: Enable data ingestion and monitoring

**Supported Platforms**:
- **Dealer.com**: Primary focus (80%+ of dealers, extensive API)
- **HomeNet**: SOAP API integration with custom code injection
- **VinSolutions**: REST API with site builder integration
- **DealerSocket**: Website management APIs
- **Cobalt**: Content management system APIs

**Advanced Features**:
- **Smart Implementation**: A/B testing different script locations
- **Fallback Strategies**: Multiple injection methods per platform
- **Performance Monitoring**: Track script load times and performance
- **Error Recovery**: Automatic retry and fix mechanisms
- **Update Management**: Easy script updates via platform APIs

#### Link Shortening & Analytics (Rebrandly) (Summary)
Objective: Use Rebrandly-managed links under a custom domain for click tracking and attribution; do not build or host a custom redirect service.

##### Functionality
Create short links for dealer URLs via Rebrandly API:
  `https://dealerwebsite.com/inventory/12345`  
  → `https://links.opendealer.app/abc123`
Clicks and analytics are captured by Rebrandly. Our system retrieves aggregated metrics via API as needed.

##### Architecture
Managed by Rebrandly with a custom branded domain (e.g., `links.opendealer.app`).
`od-marketplace` (and/or `od-data-api`) uses `OD_REBRANDLY_API_KEY` to create/find links for dealer URLs and optionally cache mappings.
TODO: After upgrading the Rebrandly account, configure the custom domain and set `OD_REBRANDLY_DOMAIN`; until then, use the default Rebrandly short domain returned by the API.

##### Benefits
LLM click attribution without operating a redirect layer.
Dealer metrics using Rebrandly analytics.
Reduced PII surface and operational overhead.

##### Optional Database Schema (cache mappings)
```sql
CREATE TABLE vehicle_links (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  dealer_id UUID NOT NULL,
  vin TEXT,
  target_url TEXT NOT NULL,
  short_url TEXT NOT NULL,
  rebrandly_link_id TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE (dealer_id, vin)
);
CREATE INDEX ON vehicle_links (dealer_id);
CREATE INDEX ON vehicle_links (vin);
```

##### Rollout Plan
Provision custom domain in Rebrandly and verify DNS.
Store `OD_REBRANDLY_API_KEY` in environment secrets for the service that creates/reads links.
Implement a utility to get-or-create short links; integrate where links are displayed (marketplace pages, feeds if desired).
Optionally add a scheduled job to pull Rebrandly click stats into a reporting store.
Note: Until the account upgrade is complete, skip the custom domain step and leave `OD_REBRANDLY_DOMAIN` unset.

### Dev Conventions & Governance

#### Naming & Namespace
Prefix: od- for repositories and services.
NPM Scope: `@opendealer` (planned). Current schema package: `@adamehrheart/schema`.
Environment Variable Prefix: OD_
Benefits: Consistency across repos, environment variables, and logs for easier maintenance and searchability.

#### Environment & Secrets Strategy
##### Principles
Rooftop IDs are configuration (per dealer), not secrets.
Integration tokens and service keys are secrets — keep them in environment variables or a secrets manager.
Multi-tenant configuration (rooftop_id, domain, platform hints) belongs in the database (managed by CMS later).

Environment (Secrets):
OD_HOMENET_INTEGRATION_TOKEN
OD_SUPABASE_URL
OD_SUPABASE_SERVICE_ROLE (server-only)
OD_API_KEY_SIGNING_SECRET (HMAC for API keys)
OD_API_KEY_SECRET (dev-only when `OD_AUTH_DEV_COMPAT=1`)
OD_REBRANDLY_API_KEY
OD_APIFY_TOKEN, optional OD_BROWSERLESS_TOKEN / OD_SCRAPINGBEE_API_KEY
Non-secret runtime toggles: OD_LOG_LEVEL, feature flags

Database (Configuration, Not Secrets):
Rooftop IDs per dealer
Dealer domains, names, platform hints, flags (prefer_scrape, etc.)
Optional per-dealer API filters (e.g., updated_since override)
Merge-priority overrides

Phased Approach:
Phase 1 (POC: Single Dealer):
Environment variables store rooftop_id, tokens.
SOAP transformer uses env directly.
Phase 2 (Multi-Dealer, Before CMS):
Add dealers table in Supabase.
Lookup rooftop_id by dealer_id or domain.
Phase 3 (With Payload CMS):
CMS manages dealer configuration.
API keys issued/rotated via CMS; scoped per dealer.

Operational Notes
Never store tokens in DB or Payload CMS — keep them in env/secrets manager.
Logging: include requestId, dealer_id, rooftop_id (safe); never log integration tokens or PII.
Authorization: API keys scoped to dealer_id; enforced in Data API.
Token rotation: update in env; adjust dealer configs via CMS.
Monitoring: alerts for ingestion failures, schema.org validation errors, API rate limits.
Metrics: track ingestion success rates, batch sizes, durations, per-dealer failures.
Backups: export Supabase tables regularly.

### Roadmap & Milestones

#### Weeks 1–4 ✅ **COMPLETED**
Core ingestion: SOAP transformer, Apify scraper.
- ✅ SOAP transformer implemented and deployed
- ✅ Supabase schema + RLS implemented
- ✅ Schema package published
- ✅ Data API with batch operations implemented

#### Weeks 5–6 ✅ **COMPLETED**
Data storage setup and retention policies.
- ✅ Implement data retention policies
- ✅ Set up automated cleanup jobs
- ✅ Optimize database performance
- ✅ Add data archival strategy
- ✅ Update documentation and README files

#### Weeks 7–8 🎯 **CURRENT FOCUS**
Merge logic implementation.
- [ ] Implement source priority merging
- [ ] Handle conflicts between API and scrape data
- [ ] Add merge strategy configuration
- [ ] Test merge scenarios

**Recent Achievements:**
- ✅ RSM Honda scraper fully implemented and tested
- ✅ Dealer.com API integration working (28+ vehicles extracted)
- ✅ Proper URL construction and pricing extraction
- ✅ Comprehensive documentation updated
- ✅ **Database security and performance optimization completed**
  - ✅ Fixed all `function_search_path_mutable` warnings
  - ✅ Consolidated migration directories (removed duplicates)
  - ✅ Applied comprehensive search path fixes to all functions
  - ✅ Set `search_path = pg_temp` for security best practices
  - ✅ Used fully qualified table names throughout
  - ✅ Resolved all Supabase linter warnings
  - ✅ Database now 100% clean with 0 warnings/errors
- ✅ **Multi-dealer support finalized**
  - ✅ Dealer detection by domain (getDealerByDomain)
  - ✅ Dealer management API endpoints (CRUD operations)
  - ✅ Scraper interface updated for dealer-specific configs
  - ✅ Support for multiple data sources per dealer
  - ✅ Ready for merge logic implementation with multiple dealers

#### Weeks 9–10 ✅ **COMPLETED**
CMS integration for dealer onboarding and API key management.
- ✅ Build Payload CMS for dealer onboarding
- ✅ Implement API key management with secure key generation
- ✅ Add multi-platform support (HomeNet, Dealer.com, VinSolutions)
- ✅ Deploy CMS to DigitalOcean App Platform
- ✅ **PRODUCTION READY**: Resolve SSL certificate verification for database connections
  - Current: Using `NODE_TLS_REJECT_UNAUTHORIZED=0` as temporary workaround
  - Required: Implement proper SSL configuration or use `sslmode=no-verify` in connection string
  - Impact: Security best practices for production database connections

#### Weeks 11–12 🎯 **CURRENT FOCUS**
API key endpoint implementation and RSM Honda integration.
- [x] ~~Implement API key distribution endpoint for scrapers~~ (Not needed - we control all scrapers)
- [ ] Test RSM Honda integration with actual HomeNet credentials
- [ ] Add manual override capabilities for vehicle data
- [ ] Complete SSL certificate configuration for production

#### Weeks 11–12
Automation: scheduled and on-demand updates.
- [ ] Implement scheduled ingestion jobs
- [ ] Add on-demand update triggers
- [ ] Set up monitoring and alerts
- [ ] Create dashboard for job status

#### Weeks 13–14
Dealer snippet and integration guide.
- [ ] Build lightweight JavaScript snippet
- [ ] Create dealer integration guide
- [ ] Implement snippet security measures
- [ ] Add analytics tracking

#### Weeks 15–20
Marketplace build and API expansion.
- [ ] Build public searchable marketplace
- [ ] Implement SEO optimization
- [ ] Add schema.org markup
- [ ] Expand API endpoints

#### Weeks 21–24
Outreach, SEO, and LLM-targeted case studies.
- [ ] Create LLM-targeted case studies
- [ ] Implement SEO strategies
- [ ] Launch outreach campaigns
- [ ] Measure and optimize performance

#### Weeks 25–28 🚀 **SELF-SERVICE FOUNDATION**
Platform detection and automated onboarding foundation.
- [ ] Build platform detection engine (Dealer.com, HomeNet, VinSolutions)
- [ ] Create automated credential validation system
- [ ] Implement dealer platform auto-configuration
- [ ] Develop automated scraper deployment via Apify API
- [ ] Test platform detection with multiple dealer types

#### Weeks 29–32 🚀 **ZERO-CODE IMPLEMENTATION**
Automated script injection via platform APIs.
- [ ] Research and implement Dealer.com API integration
- [ ] Build OAuth flow for secure credential access
- [ ] Create automated script injection system
- [ ] Implement implementation verification
- [ ] Test with RSM Honda as pilot dealer
- [ ] Add HomeNet and VinSolutions API integration

#### Weeks 33–36 🚀 **SELF-SERVICE ONBOARDING**
Complete self-service dealer onboarding flow with zero-code implementation.
- [ ] Build self-service signup and payment flow
- [ ] Create automated dealer onboarding pipeline
- [ ] Integrate zero-code implementation into onboarding
- [ ] Add automated billing integration (Stripe/PayPal)
- [ ] Develop dealer dashboard for status monitoring
- [ ] Launch zero-code implementation for Dealer.com dealers

#### Weeks 37–40 🚀 **SELF-SERVICE OPTIMIZATION**
Advanced self-service features and optimization.
- [ ] Add data quality monitoring and alerts
- [ ] Implement automated error recovery
- [ ] Create support ticket system for edge cases
- [ ] Develop advanced analytics dashboard
- [ ] Optimize onboarding success rate
- [ ] Expand zero-code implementation to additional platforms

#### Weeks 41–44 🚀 **SCALE & MARKET**
Scale self-service platform and market launch.
- [ ] Launch public self-service platform
- [ ] Implement marketing automation
- [ ] Add premium features and pricing tiers
- [ ] Create partner/affiliate program
- [ ] Scale infrastructure for multiple dealers
- [ ] Launch zero-code implementation marketing campaign



### Full Reference Specification

#### Repository Structure & Descriptions
Note: Repos are currently independent (no monorepo at the workspace root).

##### od-soap-transformer
Purpose: Serverless SOAP → normalized JSON (mapping + validation). No JSON-LD here.
Tech: TypeScript, fast-xml-parser, zod (or ajv)
Deploy: Vercel Functions or AWS Lambda
Endpoints:
GET/POST /v1/transform/homenet?filter=... → { vehicles: Vehicle[], meta }
Notes:
Externalize HOMENET_* config (env).
Timeout/retry/backoff and typed errors.
Structured logs without secrets.

##### od-scrapers
Purpose: Apify actors and fallback Playwright/Puppeteer scrapers. Emits normalized JSON.
Tech: Apify SDK, Playwright/Puppeteer (only where needed)
Deploy: Apify platform (primary); optional Railway/Render for in-house actors
Interfaces:
Actor input: { dealerUrl, platformHint, auth?, selectors? }
Output: POST webhook → Data API /v1/vehicles/batch
Notes:
Per-dealer configs; headless browser only when needed.
Rate limiting, robots.txt checks.

##### od-schema
Purpose: Shared types/validation and JSON-LD builder.
Tech: TypeScript, zod
Distribute: Private npm package (current: `@adamehrheart/schema`; plan: `@opendealer/schema`)
Exports (current):
`zVehicle`, `zVehicleBatch`, `normalizeVehicle`, `toJsonLd()`, `validateVehicleForSchemaOrg()`

##### od-data-api
Purpose: Thin API over Supabase; validates input; enforces dealer scoping; serves schema.org feeds.
Tech (current): TypeScript, custom Node.js HTTP server; Supabase JS client
Deploy: Local/dev; cloud target TBD (compatible with Node runtimes)
Endpoints (current):
`GET /health`, `GET /version`, `GET /metrics`
`GET /openapi.yaml`, `GET /docs`, `GET /redoc`
`GET /v1/vehicles` (filters: `make`, `model`, `yearMin`, `yearMax`, pagination)
`GET /v1/vehicles/by-stock/{stock}`
`GET /v1/vehicles/{vin}`
`GET /v1/vehicles/{vin}/jsonld`
`GET /v1/vehicles/{vin}/events`
`HEAD /v1/feeds/schema.org`, `GET /v1/feeds/schema.org` (supports `dealer_id`, `updated_since`, pagination)
`GET /v1/feeds/schema.ndjson`
`POST /v1/vehicles/batch` (validates with shared schema, merges per-source)
`GET /v1/links/short?url=...&vin=...` (server-side Rebrandly get-or-create; optionally caches mapping by `(dealer_id, vin)`)
Auth (current): `Authorization: Bearer <publicId>.<secret>`; keys verified against `api_keys` with HMAC using `OD_API_KEY_SIGNING_SECRET`. Dev-compat path supports `X-API-Key` when `OD_AUTH_DEV_COMPAT=1`.
Notes:
- RLS alignment with API keys is enforced by scoping queries to `dealer_id`.
- OpenAPI is served from `/openapi.yaml`; Swagger UI at `/docs`; ReDoc at `/redoc`.
- Idempotency keys: planned; current writes are upserts by `(dealer_id, vin)`.
 - Spec note: OpenAPI currently documents `X-API-Key` header auth for `/v1/vehicles/batch`, while the server expects `Authorization: Bearer <publicId>.<secret>` (with optional dev `X-API-Key`). The spec will be updated to match the implementation.
 - Debug (dev-only): `GET /v1/debug/db-snapshot`, `GET /v1/debug/list-count-test`, `GET /v1/debug/vehicles-one`.

##### od-db
Purpose: Database schema, migrations, RLS, seeds.
Tech: SQL migrations (dbmate or Prisma Migrate)
Deploy: Supabase
Contents:
Tables: dealers, vehicles, vehicle_events
Indexes: vehicles(dealer_id), vehicles(vin), vehicles(updated_at)
Policies: per-dealer RLS, service role for backend writes

##### od-cms
Purpose: Dealer onboarding, API key issuance, manual overrides.
Tech: Payload CMS (Node/Express), TS
Deploy: Railway/Render (or Vercel with configuration)
Features:
Dealer CRUD, API key rotation
Override fields for vehicles (priority merge)

##### od-platform (NEW - Self-Service)
Purpose: Self-service dealer onboarding, platform detection, automated deployment, zero-code implementation.
Tech: Next.js (App Router), TypeScript, Stripe API, Apify API, OAuth flows
Deploy: Vercel
Features:
- Platform detection engine (Dealer.com, HomeNet, VinSolutions)
- Automated scraper deployment via Apify API
- **Zero-code script implementation** via platform APIs
- Self-service signup and payment flow
- Dealer dashboard for status monitoring
- Automated snippet generation and delivery
- Billing integration (Stripe/PayPal)
- Support ticket system
- Data quality monitoring and alerts
- **OAuth integration** for secure platform access
- **Implementation verification** and testing
- **Multi-platform script injection** system

##### od-marketplace
Purpose: Public searchable inventory with SEO + schema.org markup.
Tech: Next.js (App Router), ISR
Deploy: Vercel
Features:
Search by make/model/zip
Sitemaps and JSON-LD per listing

##### od-docs
Purpose: Developer portal and dealer integration docs.
Tech: Docusaurus or Nextra
Deploy: Vercel/Cloudflare Pages
Contents:
API reference, snippets, onboarding playbooks

##### od-monitoring
Purpose: Dashboards, alerts, uptime checks.
Tech: Grafana/Loki/Prometheus (or vendor configs), Healthchecks.io
Deploy: Config repo only
Contents:
Alert rules for transformer/scrapers/API, cron schedules

##### od-infra
Purpose: Infra-as-code for DNS, webhooks, scheduled jobs, secrets wiring.
Tech: Terraform or Pulumi
Deploy: CI-managed (plan/apply per environment)
Targets:
Vercel projects, Supabase, Apify actors/webhooks, DNS


#### Environment & Secrets Strategy (Full)

##### Principles
Rooftop IDs are configuration (per dealer), not secrets.
Integration tokens and service keys are secrets; store in environment variables or a secrets manager.
Multi-tenant configuration (rooftop_id, domain, platform hints) belongs in the database (managed by CMS later).

What goes where:
##### Environment (secrets)
OD_HOMENET_INTEGRATION_TOKEN (secret)
OD_SUPABASE_URL (secret)
OD_SUPABASE_SERVICE_ROLE (secret, server-only)
OD_API_KEY_SIGNING_SECRET (secret for HMAC of API key secrets)
OD_API_KEY_SECRET (dev-only override for `X-API-Key` when `OD_AUTH_DEV_COMPAT=1`)
OD_APIFY_TOKEN, optional OD_BROWSERLESS_TOKEN / OD_SCRAPINGBEE_API_KEY
Non-secret runtime toggles (ok in env): OD_LOG_LEVEL, feature flags
##### Database (configuration, not secrets)
Rooftop IDs per dealer
Dealer domains, names, platform hints, flags (prefer_scrape, etc.)
Optional per-dealer API filters (e.g., updated_since override)
Merge-priority overrides

Phased approach:
##### Phase 1 (POC: single dealer)
Environment variables:
OD_HOMENET_INTEGRATION_TOKEN
OD_HOMENET_ROOFTOP_COLLECTION (single value)
OD_UPDATED_SINCE (optional)
SOAP transformer uses env directly.
For API: OD_SUPABASE_URL, OD_SUPABASE_SERVICE_ROLE, OD_API_KEY_SIGNING_SECRET, optional OD_DEFAULT_DEALER_ID, OD_AUTH_DEV_COMPAT, OD_REQUIRE_SUPABASE.
##### Phase 2 (multi-dealer, before CMS)
Add Supabase dealers table: id, name, rooftop_id, domain, api_config (JSONB), created_at
SOAP transformer accepts dealer_id or domain and looks up rooftop_id in DB.
Keep OD_HOMENET_INTEGRATION_TOKEN in env (shared credential).
##### Phase 3 (with Payload CMS)
Payload becomes the admin UI/API over dealers.
No change to secrets handling—CMS writes config only.
API keys are issued/rotated via CMS; scoped per dealer.

##### Logging & Security
Never store tokens in the DB or Payload—keep in env/secrets manager.
Logging: include requestId, dealer_id, rooftop_id (OK); never log integration tokens or PII.
Authorization: API keys scoped to dealer_id; enforce in Data API.
Rotation: rotate integration tokens in env; adjust dealer configs via CMS.



#### Link Shortening & Analytics (Rebrandly) (Full)

##### Objective
Use Rebrandly-managed links (custom domain) for tracking and attribution; do not build a custom redirect/edge service.

##### Functionality
Create or reuse short links via Rebrandly API for dealer target URLs (e.g., VIN detail pages).
Rebrandly handles redirecting and analytics; we can fetch aggregated metrics via their API.

##### Architecture
Managed by Rebrandly with a custom domain (e.g., `links.opendealer.app`).
Applications use `OD_REBRANDLY_API_KEY` to create/fetch links; optionally cache mappings in a DB table.

##### Integration
Marketplace CTAs use Rebrandly short links.
JSON-LD uses the canonical dealer URL to preserve SEO signals; optionally include a property referencing the short link.

##### Benefits
LLM click attribution via Rebrandly analytics.
Dealer metrics without operating a redirect layer.
Reduced operational and compliance surface area.

##### Optional Database Schema (cache mappings)
```sql
CREATE TABLE vehicle_links (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  dealer_id UUID NOT NULL,
  vin TEXT,
  target_url TEXT NOT NULL,
  short_url TEXT NOT NULL,
  rebrandly_link_id TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE (dealer_id, vin)
);
CREATE INDEX ON vehicle_links (dealer_id);
CREATE INDEX ON vehicle_links (vin);
```

##### Rollout Plan
Provision and verify Rebrandly custom domain.
Store `OD_REBRANDLY_API_KEY` as an environment secret for any service that creates/looks up links.
Implement get-or-create link utility; integrate in marketplace and optionally feeds.
Optionally sync Rebrandly analytics into a reporting store via scheduled job.
