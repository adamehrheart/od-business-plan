# Marketplace (Future)

## Goals
Public discovery with SEO‑optimized pages and LLM‑friendly outputs.

## Features
- Search: make/model/year/price/mileage, pagination
- Dealer pages: inventory summary, contact info
- Vehicle pages: specs, canonical dealer URL, images, JSON‑LD
- CTAs use Rebrandly short links; canonical remains dealer URL
- Sitemaps: dealer sitemaps and global sitemap index

## SEO & Performance
- Titles/meta/OG tags
- Schema.org Vehicle markup
- ISR/edge cache; image optimization
- Accessibility; mobile-first design
 - Canonicals: vehicle pages link to dealer VDP; avoid SEO duplication
 - Robots strategy: block classic search on stack or use canonical back to dealer

## Data Sources
- Backed by Data API reads (Phase 2)
- Completeness and freshness indicators
 - Stable URLs: `/vin/{VIN}` (HTML + JSON-LD), `/api/vehicles?updated_since=...`

## Analytics
- Click metrics via Rebrandly
- Pageview metrics via privacy‑preserving analytics (to be decided)
 - Structured data coverage and validation reports
 - Crawl frequency by allowed AI bots; alert on gaps

## Architecture
- Next.js (App Router) on Vercel
- Static + ISR blend; API hydration for filters

## Roadmap
- Phase 3: MVP search + vehicle pages + sitemaps
- Phase 4: Dealer dashboards, saved searches, alerts
 - Partnerships: submit official feeds (Google Vehicle Listings, Bing Vehicles) when applicable
