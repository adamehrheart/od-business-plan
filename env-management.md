# Environment Management

## Overview

Open Dealer uses **Doppler** for centralized environment variable management across all services. This ensures consistent configuration, secure secret management, and simplified development workflows.

## 🚀 **Current Status: Doppler Integration Complete**

All services now use Doppler for environment management:
- ✅ **Centralized Configuration**: All environment variables managed through Doppler
- ✅ **IDE Integration**: VS Code/Cursor and Warp terminal configured
- ✅ **Development Workflow**: Automated scripts for building and starting services
- ✅ **Security**: No local .env files in repositories
- ✅ **Multi-Environment**: Support for dev, staging, and production configurations

## 🔧 **Doppler Setup**

### Project Configuration
```yaml
# .doppler.yaml (project root)
setup:
  project: open-dealer
  config: dev
```

### Environment Variables by Service

#### Data API (`od-data-api`)
```bash
# Core Configuration
OD_SUPABASE_URL=postgresql://...
OD_SUPABASE_SERVICE_ROLE=eyJ...
OD_API_KEY_SIGNING_SECRET=your-signing-secret
OD_DEFAULT_DEALER_ID=5eb88852-0caa-5656-8a7b-aab53e5b1847

# Rebrandly Integration
OD_REBRANDLY_API_KEY=your-rebrandly-api-key
OD_REBRANDLY_DOMAIN=links.opendealer.app

# Development
OD_AUTH_DEV_COMPAT=1
OD_LOG_LEVEL=info
OD_DEV_SERVER=1
```

#### SOAP Transformer (`od-soap-transformer`)
```bash
# HomeNet Integration
OD_HOMENET_INTEGRATION_TOKEN=your-homenet-token
OD_HOMENET_ROOFTOP_COLLECTION=your-rooftop-id

# Development
OD_DEV_SERVER=1
OD_LOG_LEVEL=info
```

#### Scheduler (`od-scheduler`)
```bash
# Job Configuration
OD_SCHEDULER_ENABLED=1
OD_CLEANUP_ENABLED=1

# API Keys
OD_DATA_API_KEY=your-data-api-key
OD_CMS_API_KEY=your-cms-api-key

# Development
OD_LOG_LEVEL=info
```

#### CMS (`od-cms`)
```bash
# Payload Configuration
PAYLOAD_SECRET=your-payload-secret
PAYLOAD_PUBLIC_SERVER_URL=http://localhost:3000

# Database
DATABASE_URI=postgresql://...

# Development
NODE_ENV=development
```

## 🛠️ **Development Workflow**

### Starting Services
```bash
# Use the development script
./scripts/dev.sh [service]

# Available services: data-api, transformer, scheduler, cms
./scripts/dev.sh data-api
./scripts/dev.sh transformer
./scripts/dev.sh scheduler
./scripts/dev.sh cms
```

### Manual Service Startup
```bash
# Data API
cd od-data-api
doppler run -- npm run build
doppler run -- npm run start:dev

# SOAP Transformer
cd od-soap-transformer
doppler run -- npm run build
doppler run -- OD_DEV_SERVER=1 doppler run -- node dist/index.js

# Scheduler
cd od-scheduler
doppler run -- npm run build
doppler run -- node dist/src/scheduler.js

# CMS
cd od-cms
doppler run -- npm run dev
```

## 🔒 **Security Principles**

### What Goes in Doppler (Secrets)
- **Integration Tokens**: HomeNet, Rebrandly, Apify
- **Database Credentials**: Supabase URLs and service roles
- **API Keys**: Signing secrets, service-to-service keys
- **OAuth Secrets**: Platform integration credentials

### What Goes in Database (Configuration)
- **Dealer Information**: Names, domains, platform hints
- **Rooftop IDs**: Per-dealer configuration (not secrets)
- **API Filters**: Per-dealer overrides and preferences
- **Merge Priorities**: Data source preferences

### What Goes in Code (Constants)
- **Feature Flags**: Development toggles
- **Log Levels**: Debugging configuration
- **Timeouts**: Service-specific timeouts
- **Rate Limits**: API throttling configuration

## 🚫 **Legacy Environment Files (Removed)**

All legacy environment files have been removed:
- ❌ `.env` files
- ❌ `.env.example` files
- ❌ `.env.local` files
- ❌ Shell environment variables

## 🔄 **Environment Rotation**

### Integration Token Rotation
1. **Update in Doppler**: Rotate the token in Doppler dashboard
2. **Redeploy Services**: Services automatically pick up new values
3. **Verify Functionality**: Test affected integrations
4. **Update Documentation**: Update any hardcoded references

### API Key Rotation
1. **Generate New Keys**: Create new API keys in CMS
2. **Update Doppler**: Store new signing secrets
3. **Notify Dealers**: Provide new API keys to dealers
4. **Monitor Usage**: Ensure smooth transition

## 📊 **Monitoring & Logging**

### Environment Variable Validation
All services validate required environment variables on startup:
```typescript
// Example validation in od-data-api/src/env.ts
export const env = z.object({
  OD_SUPABASE_URL: z.string().url(),
  OD_SUPABASE_SERVICE_ROLE: z.string(),
  OD_API_KEY_SIGNING_SECRET: z.string().min(32),
  OD_REBRANDLY_API_KEY: z.string().optional(),
}).parse(process.env);
```

### Health Checks
Each service includes environment validation in health checks:
```bash
# Data API Health Check
curl http://localhost:3002/health

# SOAP Transformer Health Check
curl http://localhost:3001/health

# Scheduler Health Check
curl http://localhost:3003/health
```

## 🚀 **Production Deployment**

### Vercel Integration
Vercel projects automatically use Doppler environment variables:
```bash
# Vercel CLI with Doppler
vercel --env-file .env.production

# Or use Doppler directly
doppler run -- vercel --prod
```

### Digital Ocean Integration
Digital Ocean App Platform uses Doppler secrets:
```bash
# Deploy with Doppler
doppler run -- doctl apps create --spec app.yaml
```

## 📚 **IDE Integration**

### VS Code/Cursor Configuration
```json
// .vscode/settings.json
{
  "terminal.integrated.env.osx": {
    "DOPPLER_PROJECT": "open-dealer",
    "DOPPLER_CONFIG": "dev"
  },
  "files.exclude": {
    "**/.env*": true
  }
}
```

### Warp Terminal Integration
```bash
# Add to ~/.zprofile
export DOPPLER_PROJECT=open-dealer
export DOPPLER_CONFIG=dev
```

## 🔧 **Troubleshooting**

### Common Issues
1. **Doppler Not Found**: Install Doppler CLI
2. **Permission Denied**: Check Doppler project access
3. **Missing Variables**: Verify all required variables in Doppler
4. **Service Won't Start**: Check environment validation errors

### Debug Commands
```bash
# Check Doppler configuration
doppler configure

# List all environment variables
doppler secrets list

# Test environment loading
doppler run -- env | grep OD_

# Validate service environment
doppler run -- npm run validate-env
```
