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

### Platform Guides
- Dealer.com: where to place snippet, cache rules
- VinSolutions: site builder steps
- Generic HTML: copy/paste with validation checklist

## Analytics
- Clicks: Rebrandly API (by dealer_id, vin)
- Completeness: non‑null fields/total
- Freshness: age from `updated_at`
- Health: recent ingestion success/error counts

## Versioning & Rollback
- Versioned URLs (`/v1/snippets/...`)
- Canary rollouts; revert on issues

## Validation
- Structured data testing links
- Linting for required fields

## Roadmap
- Phase 3: MVP snippet + guides
- Phase 4: Self‑service generator and hosting
