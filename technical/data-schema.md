# Data Schema Documentation

## Overview

This document describes the data models and schemas used throughout the Open Dealer platform.

## Vehicle Data Model (VehicleV1)

### Core Schema
```typescript
interface VehicleV1 {
  id?: string;                    // UUID, auto-generated
  vin: string;                    // Vehicle Identification Number (17 chars)
  make: string;                   // Vehicle manufacturer
  model: string;                  // Vehicle model
  year: number;                   // Model year
  price?: number;                 // Vehicle price
  mileage?: number;               // Vehicle mileage
  fuel_type?: string;             // Fuel type (gas, electric, hybrid, etc.)
  transmission?: string;          // Transmission type
  body_style?: string;            // Body style (sedan, SUV, truck, etc.)
  drivetrain?: string;            // Drivetrain (FWD, RWD, AWD, 4WD)
  trim?: string;                  // Vehicle trim level
  stock_number?: string;          // Dealer stock number
  description?: string;           // Vehicle description
  dealerurl?: string;             // Product detail page URL
  url_source?: string;            // Source of the URL (scrape, homenet, etc.)
  images?: string[];              // Array of image URLs
  short_url?: string;             // Rebrandly short URL
  rebrandly_id?: string;          // Rebrandly link ID
  dealerslug: string;             // Dealer slug (e.g., 'rsm-honda')
  dealer_id: string;              // UUID generated from dealerslug
  source: string;                 // Data source (homenet, scrape, etc.)
  received_at?: string;           // ISO timestamp when data was received
  created_at?: string;            // ISO timestamp when record was created
  updated_at?: string;            // ISO timestamp when record was last updated
}
```

### Database Schema (PostgreSQL)
```sql
CREATE TABLE vehicles (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  vin VARCHAR(17) NOT NULL,
  make VARCHAR(100) NOT NULL,
  model VARCHAR(100) NOT NULL,
  year INTEGER NOT NULL CHECK (year >= 1900 AND year <= 2100),
  price DECIMAL(10,2),
  mileage INTEGER,
  fuel_type VARCHAR(50),
  transmission VARCHAR(50),
  body_style VARCHAR(50),
  drivetrain VARCHAR(50),
  trim VARCHAR(100),
  stock_number VARCHAR(50),
  description TEXT,
  dealerurl TEXT,
  url_source VARCHAR(50),
  images TEXT[],
  short_url TEXT,
  rebrandly_id VARCHAR(100),
  dealerslug VARCHAR(100) NOT NULL,
  dealer_id UUID NOT NULL,
  source VARCHAR(50) NOT NULL,
  received_at TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  
  -- Composite unique constraint
  UNIQUE(vin, dealerslug),
  
  -- Indexes for performance
  INDEX idx_vehicles_vin (vin),
  INDEX idx_vehicles_dealerslug (dealerslug),
  INDEX idx_vehicles_dealer_id (dealer_id),
  INDEX idx_vehicles_source (source),
  INDEX idx_vehicles_short_url (short_url),
  INDEX idx_vehicles_rebrandly_id (rebrandly_id)
);
```

## Dealer Data Model

### Core Schema
```typescript
interface Dealer {
  id: string;                     // UUID
  slug: string;                   // Unique dealer slug
  name: string;                   // Dealer name
  website: string;                // Dealer website URL
  api_key: string;                // Dealer-specific API key
  utm_config: {
    source: string;               // UTM source parameter
    medium: string;               // UTM medium parameter (default: 'LLM')
    campaign: string;             // UTM campaign parameter
    content?: string;             // UTM content parameter
    term?: string;                // UTM term parameter
  };
  platforms: {
    homenet?: {
      enabled: boolean;
      credentials: object;
    };
    dealer_com?: {
      enabled: boolean;
      url: string;
      selectors: object;
    };
  };
  created_at: string;
  updated_at: string;
}
```

## Data Flow

### 1. Data Ingestion
```
HomeNet API → SOAP Transformer → Data API → Supabase
     ↓
Dealer.com → Apify Scraper → Data API → Supabase
```

### 2. Data Processing
```
Raw Data → Mapper → Validation → Merge Logic → Storage
```

### 3. Data Access
```
Supabase → Data API → JSON-LD → LLM Discovery
```

## Data Quality Standards

### Required Fields
- `vin`: Must be 17 characters, valid VIN format
- `make`: Non-empty string
- `model`: Non-empty string
- `year`: Integer between 1900-2100
- `dealerslug`: Non-empty string, URL-safe
- `dealer_id`: Valid UUID
- `source`: Non-empty string

### Optional Fields
- All other fields are optional but should be validated when present
- `price`: Positive number
- `mileage`: Non-negative integer
- `dealerurl`: Valid URL format
- `images`: Array of valid URLs

### Data Validation
- **Zod Schema**: Runtime validation using Zod
- **Database Constraints**: PostgreSQL constraints for data integrity
- **Business Rules**: Custom validation logic for automotive data

## Data Sources

### HomeNet Integration
- **Format**: SOAP XML
- **Fields**: vin, make, model, year, price, mileage, fuel_type, transmission, body_style, drivetrain, trim, stock_number, description, images
- **Frequency**: Real-time or scheduled
- **Limitations**: No product detail URLs

### Apify Scraper
- **Format**: JSON
- **Fields**: vin, make, model, year, price, mileage, dealerurl, images, description
- **Frequency**: Scheduled (daily/hourly)
- **Purpose**: Extract product detail URLs and additional data

### Dealer.com Integration
- **Format**: HTML scraping
- **Fields**: vin, make, model, year, price, mileage, dealerurl, images
- **Frequency**: Scheduled
- **Purpose**: Vehicle listings and detail pages

## Data Merging Strategy

### Composite Key
- **Primary Key**: `(vin, dealerslug)`
- **Purpose**: Uniquely identify vehicles across dealers
- **Benefits**: Allows same VIN at different dealers

### Merge Logic
1. **Fetch Existing**: Query by composite key
2. **Merge Fields**: Non-null fields from new data take precedence
3. **Update Timestamp**: Set `updated_at` to current time
4. **Insert New**: If no existing record found

### Conflict Resolution
- **Timestamp-based**: Most recent data wins
- **Source Priority**: Scraper data preferred for URLs
- **Quality Scoring**: Higher quality data preferred

## Performance Considerations

### Indexing Strategy
- **Composite Index**: `(vin, dealerslug)` for lookups
- **Single Field Indexes**: For filtering and sorting
- **Partial Indexes**: For active vehicles only

### Caching Strategy
- **Redis Cache**: Frequently accessed data
- **CDN**: Static content and images
- **API Caching**: Response caching for performance

### Query Optimization
- **Pagination**: Limit result sets
- **Filtering**: Efficient WHERE clauses
- **Projection**: Select only needed fields

---

*This schema is designed to be flexible, performant, and maintainable while supporting the Open Dealer platform's growth and evolution.*
