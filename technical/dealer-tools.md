# Dealer Tools (Future)

## Overview
Dealer-facing assets to enable discovery and analytics with minimal implementation effort.

## Embeddable JSON-LD Snippet
- Per‑dealer generated snippet (includes canonical dealer URLs and optional UTMs)
- Versioned; integrity hash and CSP guidance
- Supports marketplace short links for CTAs (Rebrandly), but JSON‑LD uses canonical URLs

### Snippet Generator
- Inputs: dealer slug/id, base domain, UTM config
- Outputs: versioned script URL + inline JSON‑LD block

### Security
- Subresource Integrity (SRI) for scripts
- CSP recommendations; no third‑party trackers
- Minimal scope; no PII collection

### Hybrid Strategy (Dealer Site + Stack)
- Dealer site remains canonical for Google/Bing (Gemini/Copilot)
- Centralized stack optimized for AI bots (GPTBot, ClaudeBot, Perplexity) with robots allowlist
- JSON‑LD on dealer site injected via snippet/GTM; stack provides machine‑readable pages/feeds

### Robots & Sitemaps
- Dealer site robots: allow Googlebot/Bingbot; include inventory sitemaps
- Stack robots: disallow default; allow GPTBot/ClaudeBot/Perplexity; block Googlebot/Bingbot (or use canonicals)
- IndexNow: ping on create/update/sold events

### Platform Guides
- Dealer.com: where to place snippet, cache rules
- VinSolutions: site builder steps
- Generic HTML: copy/paste with validation checklist

## Analytics
- Clicks: Rebrandly API (by dealer_id, vin)
- Completeness: non‑null fields/total
- Freshness: age from `updated_at`
- Health: recent ingestion success/error counts
 - Bot hits: GPTBot/ClaudeBot/PerplexityBot logs, reverse DNS validation
 - Prompt tests: automated weekly queries in Perplexity/Copilot

## Versioning & Rollback
- Versioned URLs (`/v1/snippets/...`)
- Canary rollouts; revert on issues

## Validation
- Structured data testing links
- Linting for required fields
 - Normalization dictionary for features/options; trim/options mapping to OEM codes

## Roadmap
- Phase 3: MVP snippet + guides
- Phase 4: Self‑service generator and hosting
