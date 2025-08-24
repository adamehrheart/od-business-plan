# Async, Event-Driven Architecture

## Overview
Asynchronous, event-driven pipeline using DB-backed queues and stateless workers. Keeps ingestion fast and isolates external dependencies (scraping, Rebrandly, geocoding).

## Principles
- Source isolation (HomeNet/OEM vs Scraping)
- Idempotent tasks, retriable with exponential backoff
- Never block ingestion; enrichment is background
- Field-level merge precedence

## Queues
- job_queue: generic tasks (url_shortening, geocode, indexnow_ping, feature_normalization)
- vehicle_links: cache for PDP → short URL mappings

## Workers (Vercel Cron)
- url_shortening: verify PDP URL, create/get Rebrandly link, update cache; retries; DLQ on max attempts
- geocode: geocode dealer NAP → location (lat/lng)
- indexnow: batch-ping updates (create/update/sold)
- cleanup: prune dead tasks and old entries

## Data Model
- vehicles: core entity keyed by (dealer_id, vin | stock)
- dealers: NAP, sameAs links, `location geography(Point,4326)`
- vehicle_sources: per-source snapshots (optional)
- vehicle_links: target_url, short_url, rebrandly_id, status, attempts, last_attempt_at
- job_queue: status, attempts, next_run_at; idempotency index

## Merge Policy
- Structural/spec (make/model/year/trim): prefer HomeNet/OEM
- Surface (dealerurl/images/description): prefer Scraping
- Price: configurable (default prefer Scraping; fallback HomeNet)
- Last-write-wins only within same priority tier

## LLM/Bot Strategy
- Hybrid discovery: Dealer site for Google/Bing; Stack for GPTBot/Claude/Perplexity
- Robots: allow GPTBot/Claude/Perplexity on stack; block Google/Bing or publish canonicals
- Sitemaps & Feeds: per-type sitemaps with lastmod; JSON/NDJSON with ETag/Last-Modified
- IndexNow: fire on create/update/sold

## API Additions
- Nearby search: `lat,lng,radius` with distance sorting
- JSON-LD Car: include offeredBy PostalAddress + GeoCoordinates

## Operations & Monitoring
- Metrics: queue depth/age, success/fail %, retries, rate-limit hits
- Logs: structured with requestId, dealer_id, task_type (no secrets)
- Alerts: crawl gaps, 4xx/5xx spikes, validation errors, backlogs

## Scale Path
- Start with Supabase queues
- Scale to SQS/Pub/Sub/Cloud Tasks if needed
- Consider Temporal/Dagster only if complex dependencies emerge
