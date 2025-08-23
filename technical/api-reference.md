# API Reference Documentation

## Overview

This document provides comprehensive API documentation for the Open Dealer platform, including endpoints, authentication, request/response formats, and examples.

## Base URLs

### Production
- **Data API**: `https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app`
- **JSON-LD API**: `https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app`
- **Sitemap API**: `https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app`

### Development
- **Data API**: `http://localhost:3000`
- **JSON-LD API**: `http://localhost:3000`
- **Sitemap API**: `http://localhost:3000`

## Authentication

### API Key Authentication
All API endpoints require authentication using dealer-specific API keys.

#### Headers Required
```
X-API-Key: your-dealer-api-key
X-Dealer-ID: your-dealer-slug
```

#### Example
```bash
curl -X POST https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/vehicles/batch \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key" \
  -H "X-Dealer-ID: rsm-honda" \
  -d '{"vehicles": [...]}'
```

## Data API Endpoints

### POST /api/v1/vehicles/batch

Ingest vehicle data from external sources (HomeNet, scrapers, etc.).

#### Request Format
```json
{
  "vehicles": [
    {
      "vin": "1HGBH41JXMN109186",
      "make": "Honda",
      "model": "Civic",
      "year": 2023,
      "price": 25000,
      "mileage": 15000,
      "fuel_type": "gas",
      "transmission": "automatic",
      "body_style": "sedan",
      "drivetrain": "FWD",
      "trim": "EX",
      "stock_number": "H23-001",
      "description": "2023 Honda Civic EX with advanced features",
      "dealerurl": "https://www.rsmhondaonline.com/new/Honda/2023-Civic-EX",
      "images": [
        "https://example.com/image1.jpg",
        "https://example.com/image2.jpg"
      ]
    }
  ],
  "source": "homenet"
}
```

#### Response Format
```json
{
  "success": true,
  "processed": 1,
  "dealer_id": "rsm-honda",
  "source": "homenet",
  "timestamp": "2025-01-23T13:00:00Z"
}
```

#### Error Response
```json
{
  "success": false,
  "processed": 0,
  "dealer_id": "rsm-honda",
  "source": "homenet",
  "errors": [
    {
      "vin": "1HGBH41JXMN109186",
      "error": "Invalid VIN format"
    }
  ],
  "timestamp": "2025-01-23T13:00:00Z"
}
```

### GET /api/v1/vehicles

Retrieve vehicle data for a specific dealer.

#### Query Parameters
- `dealer_id` (required): Dealer slug
- `limit` (optional): Number of results (default: 100, max: 1000)
- `offset` (optional): Pagination offset (default: 0)
- `make` (optional): Filter by make
- `model` (optional): Filter by model
- `year` (optional): Filter by year
- `price_min` (optional): Minimum price
- `price_max` (optional): Maximum price

#### Example Request
```bash
curl "https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/vehicles?dealer_id=rsm-honda&limit=50&make=Honda"
```

#### Response Format
```json
{
  "vehicles": [
    {
      "id": "uuid-here",
      "vin": "1HGBH41JXMN109186",
      "make": "Honda",
      "model": "Civic",
      "year": 2023,
      "price": 25000,
      "mileage": 15000,
      "fuel_type": "gas",
      "transmission": "automatic",
      "body_style": "sedan",
      "drivetrain": "FWD",
      "trim": "EX",
      "stock_number": "H23-001",
      "description": "2023 Honda Civic EX with advanced features",
      "dealerurl": "https://www.rsmhondaonline.com/new/Honda/2023-Civic-EX",
      "short_url": "https://links.opendealer.app/v1/llm/rsm-honda/1HGBH41JXMN109186",
      "images": [
        "https://example.com/image1.jpg",
        "https://example.com/image2.jpg"
      ],
      "dealerslug": "rsm-honda",
      "dealer_id": "uuid-here",
      "source": "homenet",
      "created_at": "2025-01-23T13:00:00Z",
      "updated_at": "2025-01-23T13:00:00Z"
    }
  ],
  "pagination": {
    "total": 150,
    "limit": 50,
    "offset": 0,
    "has_more": true
  }
}
```

## JSON-LD API Endpoints

### GET /api/v1/jsonld/vehicle/{vin}

Get structured data for a specific vehicle in JSON-LD format.

#### Example Request
```bash
curl "https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/jsonld/vehicle/1HGBH41JXMN109186"
```

#### Response Format
```json
{
  "@context": "https://schema.org",
  "@type": "Car",
  "name": "2023 Honda Civic EX",
  "brand": {
    "@type": "Brand",
    "name": "Honda"
  },
  "model": "Civic",
  "vehicleModelDate": "2023",
  "mileageFromOdometer": {
    "@type": "QuantitativeValue",
    "value": 15000,
    "unitCode": "SMI"
  },
  "vehicleCondition": "https://schema.org/UsedCondition",
  "offers": {
    "@type": "Offer",
    "price": 25000,
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "seller": {
      "@type": "AutoDealer",
      "name": "RSM Honda",
      "url": "https://www.rsmhondaonline.com"
    }
  },
  "url": "https://www.rsmhondaonline.com/new/Honda/2023-Civic-EX"
}
```

### GET /api/v1/jsonld/dealer/{dealer_id}

Get structured data for a dealer in JSON-LD format.

#### Example Request
```bash
curl "https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/jsonld/dealer/rsm-honda"
```

#### Response Format
```json
{
  "@context": "https://schema.org",
  "@type": "AutoDealer",
  "name": "RSM Honda",
  "url": "https://www.rsmhondaonline.com",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Main Street",
    "addressLocality": "San Diego",
    "addressRegion": "CA",
    "postalCode": "92101",
    "addressCountry": "US"
  },
  "telephone": "+1-555-123-4567",
  "openingHours": "Mo-Fr 09:00-18:00, Sa 09:00-17:00"
}
```

## Sitemap API Endpoints

### GET /api/v1/sitemap/{dealer_id}.xml

Get XML sitemap for a specific dealer.

#### Example Request
```bash
curl "https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/sitemap/rsm-honda.xml"
```

#### Response Format
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://www.rsmhondaonline.com/new/Honda/2023-Civic-EX</loc>
    <lastmod>2025-01-23T13:00:00Z</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://www.rsmhondaonline.com/new/Honda/2023-CRV-EX</loc>
    <lastmod>2025-01-23T13:00:00Z</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

### GET /api/v1/sitemap/{dealer_id}.json

Get JSON sitemap for a specific dealer.

#### Example Request
```bash
curl "https://od-data-ird8o7nf1-adam-ehrhearts-projects.vercel.app/api/v1/sitemap/rsm-honda.json"
```

#### Response Format
```json
{
  "sitemap": [
    {
      "url": "https://www.rsmhondaonline.com/new/Honda/2023-Civic-EX",
      "lastmod": "2025-01-23T13:00:00Z",
      "changefreq": "daily",
      "priority": 0.8
    },
    {
      "url": "https://www.rsmhondaonline.com/new/Honda/2023-CRV-EX",
      "lastmod": "2025-01-23T13:00:00Z",
      "changefreq": "daily",
      "priority": 0.8
    }
  ]
}
```

## Error Handling

### HTTP Status Codes
- `200 OK`: Request successful
- `400 Bad Request`: Invalid request format or parameters
- `401 Unauthorized`: Invalid or missing API key
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error

### Error Response Format
```json
{
  "error": {
    "code": "INVALID_VIN",
    "message": "Invalid VIN format",
    "details": "VIN must be exactly 17 characters",
    "timestamp": "2025-01-23T13:00:00Z"
  }
}
```

## Rate Limiting

### Limits
- **Data API**: 1000 requests per hour per dealer
- **JSON-LD API**: 5000 requests per hour per dealer
- **Sitemap API**: 100 requests per hour per dealer

### Rate Limit Headers
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1642953600
```

## SDKs and Libraries

### JavaScript/TypeScript
```bash
npm install @opendealer/api-client
```

```javascript
import { OpenDealerAPI } from '@opendealer/api-client';

const api = new OpenDealerAPI({
  apiKey: 'your-api-key',
  dealerId: 'rsm-honda'
});

// Ingest vehicles
const result = await api.ingestVehicles(vehicles, 'homenet');

// Get vehicles
const vehicles = await api.getVehicles({ limit: 50, make: 'Honda' });
```

### Python
```bash
pip install opendealer-api
```

```python
from opendealer import OpenDealerAPI

api = OpenDealerAPI(api_key='your-api-key', dealer_id='rsm-honda')

# Ingest vehicles
result = api.ingest_vehicles(vehicles, source='homenet')

# Get vehicles
vehicles = api.get_vehicles(limit=50, make='Honda')
```

## Webhooks

### Vehicle Updates
Receive real-time notifications when vehicle data is updated.

#### Webhook URL
```
POST https://your-endpoint.com/webhooks/vehicles
```

#### Payload Format
```json
{
  "event": "vehicle.updated",
  "dealer_id": "rsm-honda",
  "vehicle": {
    "vin": "1HGBH41JXMN109186",
    "make": "Honda",
    "model": "Civic",
    "year": 2023,
    "price": 25000,
    "updated_at": "2025-01-23T13:00:00Z"
  },
  "timestamp": "2025-01-23T13:00:00Z"
}
```

---

*This API reference provides comprehensive documentation for integrating with the Open Dealer platform.*
