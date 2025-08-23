# Strategic Context (SVPG)

Version: 0.1 • Owner: Product • Last updated: $(date +%Y-%m-%d)

## Product Mission
Make every dealership instantly discoverable by AI and search—without ongoing manual work—by automating structured data, feeds, and discovery signals that route shoppers back to the dealer’s own site.

## Product Vision
An autonomous platform that ingests any dealer’s inventory, normalizes it into a canonical model, and continuously publishes LLM‑ready and search‑ready surfaces (APIs, JSON‑LD, sitemaps), with measurable traffic and revenue impact.

## Product Principles
- Outcomes over outputs; traffic and conversion back to the dealer’s site
- Automation first; no bespoke services or agency work
- Security and privacy by default; least privilege and minimal data
- Versioned, measurable, observable systems
- Open integrations; no lock‑in (canonical links to dealer VDPs)

## Product Strategy (How we win)
1. Hybrid discovery: dealer site remains canonical for Google/Bing; our stack powers AI assistants
2. Quality and freshness: canonical schema + merge logic + sub‑5‑minute publishing
3. Distribution signals: robots, sitemaps, IndexNow, and partner feeds (Google Vehicle Listings/Bing)
4. Self‑service at scale: platform detection, automated scrapers, zero‑code snippet, and dashboards
5. Proof and trust: analytics (clicks, completeness, freshness) and bot monitoring to demonstrate impact

## 1) Purpose (Why now)
Enable local dealerships to become AI/LLM‑discoverable without ongoing manual SEO by providing a centralized, normalized data stack and lightweight dealer tooling.

## Product Objectives & OKRs (12–18 months)
Objectives
- O1: Establish Open Dealer as the de facto AI discovery layer for local dealer inventory
- O2: Prove measurable traffic impact back to dealer sites
- O3: Deliver a self‑service platform that scales without services

Key Results (illustrative—validate with pilots)
- KR1: 100 live rooftops; 1,000 by year 2
- KR2: Median freshness < 5 minutes; P95 < 15 minutes
- KR3: > 95% valid JSON‑LD; > 90% completeness across core fields
- KR4: ≥ 10% of inventory sessions attributable to AI/LLM sources
- KR5: NPS ≥ 50; time‑to‑first‑publish ≤ 1 day from signup

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

## Product Scorecard (Core KPIs)
Discovery
- AI bot crawl frequency/day (GPTBot, ClaudeBot, Perplexity)
- Dealer site vs stack citation ratio (Perplexity/Copilot tests)

Freshness & Quality
- Median and P95 ingestion → publish latency
- JSON‑LD validation pass rate; field completeness

Traffic & Adoption
- Rebrandly short‑link clicks → dealer sessions
- Rooftops onboarded/month; time‑to‑first‑publish

Reliability
- Uptime; failed ingestion jobs/day; retry success rate

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
