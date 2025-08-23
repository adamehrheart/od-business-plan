# Strategic Context (SVPG)

Version: 0.1 • Owner: Product • Last updated: $(date +%Y-%m-%d)

## 1) Purpose (Why now)
Enable local dealerships to become AI/LLM‑discoverable without ongoing manual SEO by providing a centralized, normalized data stack and lightweight dealer tooling.

## 2) Business Objectives (12–18 months)
- Revenue: $500K ARR (Year 1), $2M ARR (Year 2) [illustrative; validate post‑pilots]
- Distribution: 100 live rooftops → 1,000 rooftops
- Time‑to‑freshness: < 5 minutes from source change to public API/feed
- Reliability: 99.9% uptime; < 0.1% ingestion failure rate/day
- Quality: > 95% JSON‑LD validation pass rate; > 90% field completeness

## 3) Target Customers & Problems
- Primary: US franchised and independent auto dealerships (marketing/GM/owner)
- Pain: Low AI discovery; brittle/slow vendor sitemaps; inconsistent schema; high SEO overhead
- Need: “Set‑and‑forget” automation that drives qualified traffic to dealer VDPs

## 4) Market Landscape (Directionally sourced)
- NADA Data: ~16–18K franchised new‑car dealerships in US (latest NADA Data)
- LLM crawlers (GPTBot, ClaudeBot, PerplexityBot) discover open web content respecting robots/sitemaps
- Preferred placements often require formal feeds (Google Vehicle Listings, Bing programs)
- See `business/market-analysis.md` for sources and current citations

## 5) Value Proposition
“Automate your site’s SEO and AI discoverability — zero human effort.”
- Normalize & validate inventory data
- Publish LLM‑ready JSON‑LD + feeds with fast freshness
- Keep dealer site canonical for Google/Bing; use our stack for AI assistants
- Provide click analytics via Rebrandly; merge data across sources by VIN+dealer

## 6) Product Principles (Guardrails)
- Traffic flows back to dealer’s canonical VDPs (no lock‑in)
- Automation over services; no bespoke SEO engagements
- Versioned, documented, measurable APIs and feeds
- Privacy‑preserving analytics; least‑privilege access
- Observability first: logs, metrics, validation at every stage

## 7) Constraints & Assumptions
- We do not modify third‑party dealer platforms beyond documented methods (snippet/GTM/API)
- Rebrandly provides link management; no custom redirect layer
- Supabase/Postgres as the system of record; serverless for APIs and schedulers
- Authentication via per‑dealer API keys; CMS for key lifecycle (Phase 2)

## 8) Success Metrics (North Stars & Levers)
- Discovery: AI bot crawl frequency (GPTBot/Claude/Perplexity) per dealer/day
- Freshness: median end‑to‑end latency to publish
- Quality: % valid JSON‑LD; field completeness per dealer
- Traffic: short‑link clicks → dealer sessions (attribution)
- Adoption: rooftops onboarded/month; time‑to‑first‑publish

## 9) Strategic Bets
- Hybrid discovery model (dealer site canonical; stack for AI)
- Deterministic IDs + merge by (vin, dealerslug) to unify sources
- Robots/IndexNow as first‑class distribution signals
- Partner feeds (Google Vehicle Listings/Bing) to unlock preferred placement

## 10) Scope & Out‑of‑Scope
- In scope: ingestion, normalization, merge, JSON‑LD/feeds, marketplace, analytics
- Out of scope: agency SEO/content writing, custom redirect infra, complex CMS builds

## 11) Operating Model (High level)
- Empowered, cross‑functional squads aligned to outcomes (Ingestion, Feeds, Marketplace)
- Dual‑track discovery/delivery; weekly cadence; quarterly objectives
- Roadmap by phases (see `ROADMAP.md`), measured by defined KPIs

## 12) Dependencies & Risks
- Platform variability (Dealer.com, VinSolutions) → mitigate via Apify and per‑platform selectors
- Authentication sprawl → centralize via CMS, env management
- LLM/search policy changes → keep robots/canonicals configurable per dealer

## 13) Near‑Term Priorities (Next 4–8 weeks)
- Finish Phase 1 DoD (pilot dealer reliability, merge logic hardening)
- Implement reads/feeds MVP: GET /api/v1/vehicles + JSON‑LD (Phase 2)
- Wire IndexNow + stack robots policy; add bot monitoring dashboard

## 14) References
- Roadmap: `ROADMAP.md`
- Architecture: `architecture/overview.md`
- Dealer Tools: `technical/dealer-tools.md`
- Marketplace: `technical/marketplace.md`
- Strategic transcript: `references/strategic-gpt-conversations.md`
- Market analysis & sources: `business/market-analysis.md`
