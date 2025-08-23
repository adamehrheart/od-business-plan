# Self-Service Onboarding (Zero-Code Implementation)

## Overview
Enable dealers to connect platform credentials and have Open Dealer automatically deploy scrapers, inject snippets (where supported), verify implementation, and activate ingestion without human intervention.

## Vision
Dealer visits `opendealer.app` → platform detection → pricing shown → secure payment → credential collect/validate → automated implementation → verification → data flowing.

## Core Capabilities
- Platform Detection: fingerprint Dealer.com, HomeNet, VinSolutions patterns
- Automated Scraper Deployment: Apify templates per platform; scheduled runs
- Snippet Generation: dealer‑specific JSON‑LD + config; versioned
- Implementation Verification: script load, JSON‑LD validation, data freshness
- Billing & Entitlements: Stripe subscription and setup fee (tiers), service gating

## Flows
### Pre‑Implementation
1. Detect platform (scan known API/HTML patterns)
2. Show pricing (by platform complexity/vehicle volume)
3. Collect payment (Stripe; card tokenization; subscription)
4. Create dealer record and entitlements

### Automated Implementation
1. Validate credentials (platform specific)
2. Deploy scraper (Apify API) with dealer config
3. Generate snippet (or GTM payload) and provide placement guidance
4. Verify implementation (script presence, JSON‑LD valid, events in logs)
5. Activate ingestion and schedule

## Security
- OAuth/token storage with least privilege; no raw card data
- Audit logging for admin actions; no PII in logs
- CSP/SRI guidance for snippets; no third‑party trackers

## Pricing (Illustrative)
- Basic: $149/mo (automated, standard support)
- Standard: $199/mo (automated, analytics)
- Enterprise: custom (priority support, SLAs)
- Optional setup fee for manual platforms

## Milestones
- Phase 1: Platform detection + Dealer.com research; verification harness
- Phase 2: HomeNet/VinSolutions support; unified implementer interface
- Phase 3: Dealer portal (status, billing, troubleshooting)

## Risks & Mitigations
- Platform API access: start with public docs; apply for dev access; fall back to GTM
- Rate limits: adaptive backoff; job scheduling
- Variations per rooftop: template overrides; selectors configurable in CMS
- Verification: synthetic checks and real page tests

## KPIs
- Time to first publish (signup → data live)
- Implementation success rate
- Support tickets per onboarding
- Conversion and churn (billing)

## References
- Roadmap: `../../ROADMAP.md`
- Dealer Tools: `./dealer-tools.md`
- Strategic transcript: `../references/strategic-gpt-conversations.md`
- Zero‑Code research (incorporated): billing, platform detection, OAuth, pricing
