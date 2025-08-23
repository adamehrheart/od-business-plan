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
- `DATABASE_URL` - PostgreSQL connection string
- `OD_CMS_URL` - CMS API URL
- `OD_DATA_API_URL` - Data API URL
- `OD_SOAP_TRANSFORMER_URL` - SOAP Transformer URL
- `OD_BEARER_TOKEN` - API authentication token
- `OD_API_KEY_SECRET` - API key for Data API authentication

### **5. od-docs (Vercel)**
- `VERCEL_USERNAME` - Basic auth username
- `VERCEL_PASSWORD` - Basic auth password

## 🔧 **Management Strategies**

### **Strategy 1: Vercel Environment Variables (Recommended)**

Use Vercel's built-in environment variable management:

```bash
# List all environment variables for a project
vercel env ls

# Add environment variable to all projects
vercel env add VARIABLE_NAME --scope=team

# Pull environment variables to local .env
vercel env pull .env.local
```

### **Strategy 2: Centralized .env Template**

Create a master `.env.template` file:

```bash
# Create master template
cat > .env.template << 'EOF'
# Database
DATABASE_URL=postgresql://...
DATABASE_URI=postgresql://...

# CMS
PAYLOAD_SECRET=your-secret-here
PAYLOAD_PUBLIC_SERVER_URL=https://od-cms-vyw5b.ondigitalocean.app

# APIs
OD_CMS_URL=https://od-cms-vyw5b.ondigitalocean.app
OD_DATA_API_URL=https://od-data-mw2q53faw-adam-ehrhearts-projects.vercel.app
OD_SOAP_TRANSFORMER_URL=https://od-soap-transformer-1apzh4czq-adam-ehrhearts-projects.vercel.app

# HomeNet
OD_HOMENET_INTEGRATION_TOKEN=04f5a88f-6776-457f-bae1-f0256b03eb54
OD_HOMENET_ROOFTOP_COLLECTION=13157

# Rebrandly
OD_REBRANDLY_ACCOUNT_ID=your-account-id
OD_REBRANDLY_API_KEY=your-api-key

# Supabase
OD_SUPABASE_URL=your-supabase-url
OD_SUPABASE_SERVICE_ROLE=your-service-role-key

# Authentication
OD_BEARER_TOKEN=opndlr_live_memv6bca310c5b9ff5be31a3.Y9qrg6BTlxXyxqnq9iXO9WlvAXNRGHP1
OD_API_KEY_SECRET=od-secret-key-2024-08-22
EOF
```

### **Strategy 3: Environment Management Script**

Create a script to sync environment variables across projects:

```bash
#!/bin/bash
# sync-env.sh - Sync environment variables across projects

PROJECTS=("od-data-api" "od-soap-transformer" "od-scheduler" "od-docs")

# Function to sync a variable across all projects
sync_variable() {
    local var_name=$1
    local var_value=$2
    
    echo "Syncing $var_name across all projects..."
    
    for project in "${PROJECTS[@]}"; do
        echo "  Adding to $project..."
        cd "../$project"
        echo "$var_value" | vercel env add "$var_name" --scope=team
        cd ..
    done
}

# Sync common variables
sync_variable "OD_CMS_URL" "https://od-cms-vyw5b.ondigitalocean.app"
sync_variable "OD_DATA_API_URL" "https://od-data-mw2q53faw-adam-ehrhearts-projects.vercel.app"
sync_variable "OD_SOAP_TRANSFORMER_URL" "https://od-soap-transformer-1apzh4czq-adam-ehrhearts-projects.vercel.app"
```

## 🚀 **Recommended Approach**

1. **Use Vercel Environment Variables** for all Vercel projects
2. **Create a master .env.template** for documentation
3. **Use environment management scripts** for bulk operations
4. **Document all variables** in this file

## 📝 **Next Steps**

1. Create the master `.env.template` file
2. Set up environment management scripts
3. Document all current environment variables
4. Implement automated syncing for common variables
