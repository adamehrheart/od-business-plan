# Open Dealer Environment Management

## 🎯 **Centralized Environment Variables Management**

This document provides a centralized approach to managing environment variables across all Open Dealer projects.

## 📋 **Current Projects & Their Environment Variables**

### **1. od-cms (Digital Ocean)**
- `DATABASE_URI` - PostgreSQL connection string
- `PAYLOAD_SECRET` - PayloadCMS encryption key
- `PAYLOAD_PUBLIC_SERVER_URL` - Public CMS URL

### **2. od-data-api (Vercel)**
- `OD_SUPABASE_URL` - Supabase project URL
- `OD_SUPABASE_SERVICE_ROLE` - Supabase service role key
- `OD_CMS_URL` - CMS API URL
- `OD_REBRANDLY_ACCOUNT_ID` - Rebrandly account ID
- `OD_REBRANDLY_API_KEY` - Rebrandly API key
- `OD_API_KEY_SECRET` - API key for vehicle ingestion authentication

### **3. od-soap-transformer (Vercel)**
- `OD_HOMENET_INTEGRATION_TOKEN` - HomeNet API token
- `OD_HOMENET_ROOFTOP_COLLECTION` - HomeNet rooftop ID
- `OD_UPDATED_SINCE` - Default date filter (optional)

### **4. od-scheduler (Vercel)**
- `OD_SUPABASE_URL` - Supabase project URL
- `OD_SUPABASE_SERVICE_ROLE` - Supabase service role key
- `OD_CMS_URL` - CMS API URL
- `OD_DATA_API_URL` - Data API URL
- `OD_SOAP_TRANSFORMER_URL` - SOAP Transformer URL
- `OD_BEARER_TOKEN` - API authentication token
- `OD_API_KEY_SECRET` - API key for Data API authentication

### **5. od-docs (Vercel)**
- `VERCEL_USERNAME` - Basic auth username
- `VERCEL_PASSWORD` - Basic auth password

## 🔧 **Management Strategies**

### **Strategy 1: Vercel Environment Variables (Current)**

Use Vercel's built-in environment variable management:

```bash
# List all environment variables for a project
vercel env ls

# Add environment variable to a project
vercel env add VARIABLE_NAME --scope=team

# Pull environment variables to local .env
vercel env pull .env.local
```

### **Strategy 2: Centralized .env Template**

Create a master `.env.template` file for documentation only.

### **Strategy 3: Environment Management Script**

Use `scripts/sync-env.sh` to push common variables to Vercel across projects.

## ✅ Recommended Backbone: Doppler + Per-Repo Validation

- **Doppler** will be the single source of truth for secrets and envs.
- **Zod** schemas in each repo validate required keys at startup (no secrets stored in code).

### Doppler Setup
1. Install CLI (done): `brew install dopplerhq/cli/doppler`
2. Login (one-time, interactive): `doppler login`
3. Create project and configs:
   - Project: `open-dealer`
   - Configs: `dev`, `staging`, `prod`
4. Create services (suggested secrets folders):
   - `od-data-api`, `od-soap-transformer`, `od-scheduler`, `od-docs`, `od-cms`
5. Import variables from `.env.example` files for each repo; set actual values in Doppler.

### Usage
- Local dev:
```bash
doppler setup  # select project/config once
doppler run -- npm run dev  # inject envs at runtime
```
- CI:
```yaml
- uses: dopplerhq/cli-action@v2
- run: doppler run -- npm run build
```
- Vercel:
  - Use Doppler→Vercel integration or periodic sync: `doppler secrets download --no-file --format env > .env && vercel env import .env`

### Per-Repo Validation (Zod)
- `od-data-api/src/env.ts`
- `od-scheduler/src/env.ts`
- `od-soap-transformer/src/env.ts`

These enforce required keys (fail fast) but do not store secrets.

### Naming Conventions
- Prefix: `OD_*`
- Consistent names across repos (no `DB_URL` vs `DATABASE_URL` duplicates)

### Rotation
- Rotate in Doppler → sync to Vercel/DO → restart deploys.
