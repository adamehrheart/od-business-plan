# Technical Architecture Overview

## System Architecture

Open Dealer's architecture is designed around a modern, serverless-first approach that prioritizes scalability, reliability, and maintainability. The system is built to handle real-time data processing, provide high availability, and support rapid feature development.

## Core Components

### 1. Data Ingestion Layer

#### HomeNet Integration
- **Purpose**: Extract vehicle data from dealer inventory systems
- **Technology**: SOAP API integration via custom transformer
- **Frequency**: Real-time or scheduled (configurable)
- **Data Types**: Vehicle specifications, pricing, availability

#### Apify Scraper
- **Purpose**: Extract vehicle detail page URLs and additional data
- **Technology**: Enterprise-grade web scraping platform
- **Frequency**: Scheduled runs (daily/hourly)
- **Data Types**: Product detail URLs, images, descriptions

### 2. Data Processing Layer

#### Canonical Schema (VehicleV1)
```typescript
interface VehicleV1 {
  id?: string;
  vin: string;
  make: string;
  model: string;
  year: number;
  price?: number;
  mileage?: number;
  fuel_type?: string;
  transmission?: string;
  body_style?: string;
  drivetrain?: string;
  trim?: string;
  stock_number?: string;
  description?: string;
  dealerurl?: string;
  url_source?: string;
  images?: string[];
  short_url?: string;
  rebrandly_id?: string;
  dealerslug: string;
  dealer_id: string;
  source: string;
  received_at?: string;
  created_at?: string;
  updated_at?: string;
}
```

#### Data Mappers
- **HomeNet Mapper**: Transform SOAP XML to VehicleV1
- **Scraper Mapper**: Transform web scraped data to VehicleV1
- **Validation**: Zod schema validation for data quality

#### Merge Logic
- **Composite Key**: `(vin, dealerslug)` for unique identification
- **Merge Strategy**: Non-null fields from new data take precedence
- **Conflict Resolution**: Timestamp-based updates

### 3. Data Storage Layer

#### Supabase (PostgreSQL)
- **Primary Database**: Vehicle and dealer data storage
- **Real-time Features**: Live data updates
- **Row Level Security**: Data isolation by dealer
- **Backup Strategy**: Automated daily backups

### 4. API Layer

#### Data API (Vercel Serverless)
- **Endpoint**: `/api/v1/vehicles/batch`
- **Authentication**: API key + dealer ID
- **Rate Limiting**: Per-dealer quotas
- **Response Format**: JSON with error handling

#### JSON-LD API (Planned)
- **Status**: Planned (Phase 2)
- **Purpose**: Structured data for LLM consumption
- **Schema.org**: Vehicle and dealer markup
- **Caching**: Aggressive caching for performance
- **Format**: Machine-readable JSON-LD

### 5. Discovery Layer

#### Sitemap Generation (Planned)
- **Status**: Planned (Phase 3)
- **Automated Creation**: Real-time sitemap updates
- **Multiple Formats**: XML, JSON, RSS feeds
- **Priority Management**: Vehicle-specific priorities
- **Last Modified**: Dynamic timestamp updates

#### IndexNow Integration
- **Real-time Pinging**: Immediate URL submission
- **Batch Processing**: Efficient bulk submissions
- **Error Handling**: Retry logic for failed submissions
- **Monitoring**: Success/failure tracking

## Deployment Architecture

### Production Stack

#### Vercel (Serverless Functions)
- **Edge Deployment**: Global CDN distribution
- **Auto-scaling**: Automatic resource allocation
- **Zero Downtime**: Blue-green deployments
- **Monitoring**: Built-in performance monitoring

#### Digital Ocean (PayloadCMS)
- **App Platform**: Managed container deployment
- **Auto-scaling**: Horizontal scaling capabilities
- **SSL/TLS**: Automatic certificate management
- **Backup**: Automated database backups

#### Supabase (Database)
- **Managed PostgreSQL**: Fully managed database
- **Real-time**: WebSocket connections
- **Auth**: Built-in authentication system
- **Storage**: File storage and CDN

#### Apify (Web Scraping)
- **Enterprise Platform**: Reliable scraping infrastructure
- **Scheduling**: Automated job execution
- **Monitoring**: Performance and success tracking
- **Scaling**: Automatic resource scaling

## Technology Stack

### Frontend
- **React**: User interface framework
- **TypeScript**: Type-safe development
- **Tailwind CSS**: Utility-first styling
- **Vite**: Fast build tool

### Backend
- **Node.js**: Server-side runtime
- **TypeScript**: Type-safe development
- **Express**: Web framework
- **Zod**: Schema validation

### Database
- **PostgreSQL**: Primary database
- **Redis**: Caching layer
- **Supabase**: Database-as-a-service

### Infrastructure
- **Vercel**: Serverless platform
- **Digital Ocean**: Cloud hosting
- **Apify**: Web scraping platform
- **GitHub**: Version control

---

*This architecture is designed to be scalable, maintainable, and future-proof while providing the foundation for Open Dealer's growth and success.*
