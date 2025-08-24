# Open Dealer Scheduler - TODO

## Current Work
- [x] URL shortening system with Rebrandly integration
- [x] Asynchronous job queue architecture
- [x] Sitemap processor for RSM Honda
- [x] **COMPLETED: Apify Vehicle Scraper Actor** - Comprehensive vehicle data scraping with sitemap processing and page extraction
- [ ] Fix job processing "Unknown error" issue
- [ ] **NEW: Deploy and test Apify actor** - Deploy to Apify platform and verify integration

## Next Steps
- [ ] **Apify Integration Testing**: Test the deployed actor with RSM Honda
- [ ] **Apify Scheduling**: Set up daily/weekly automated runs
- [ ] **Data API Webhook**: Create endpoint to receive Apify results automatically
- [ ] **Multi-Dealer Support**: Extend Apify actor for other Dealer.com dealerships
- [ ] Geocoding worker for dealers without location
- [ ] Data API: nearby search (lat,lng,radius) + distance in results
- [ ] Feeds/JSON-LD: offeredBy PostalAddress + GeoCoordinates
- [ ] Distribution: sitemaps per type + IndexNow pinger job
- [ ] Robots split: stack bots vs Google/Bing; add canonicals if needed
- [ ] Monitoring: queue depth/age tiles in CMS + bot logs dashboard

## Dealer.com Inventory Feed Investigation
- [x] **Verify RSM Honda inventory feed**: Check if the detailed JSON feed we discovered is still available
- [x] **Test other Dealer.com sites**: Verify if this feed architecture is consistent across all Dealer.com dealerships
- [x] **Assess feed efficiency**: Compare single JSON feed vs individual product detail page scraping
- [x] **Design async architecture**: If feed exists, create new batch process for inventory data ingestion
- [x] **Fallback strategy**: If feed unavailable, enhance product detail page scraping with Dealer.com object extraction

## Architecture Enhancements
- [x] **Primary**: Dealer.com inventory feed processor (if available)
- [x] **Secondary**: Enhanced product detail page scraper with Dealer.com object extraction
- [x] **Integration**: Connect inventory feed data with existing HomeNet data for comprehensive vehicle profiles
- [x] **Apify Integration**: Scalable, automated vehicle data scraping with comprehensive data extraction

## Apify Implementation Status
- [x] **Actor Development**: Complete vehicle scraper with sitemap processing
- [x] **Data Extraction**: VIN, make, model, year, pricing, features, images
- [x] **Configuration**: Flexible input parameters for different dealers
- [x] **Integration Scripts**: Process results and send to Data API
- [x] **Documentation**: Comprehensive setup and integration guide
- [ ] **Deployment**: Deploy to Apify platform
- [ ] **Testing**: Verify with RSM Honda data
- [ ] **Scheduling**: Set up automated runs
- [ ] **Monitoring**: Track performance and data quality

## Recent Achievements
- ✅ **Apify Actor Complete**: Full-featured vehicle scraper with comprehensive data extraction
- ✅ **Hybrid URL Strategy**: Combined sitemap and Dealer.com feed for optimal URL discovery
- ✅ **VIN-to-URL Mapping**: Successfully mapped 98 vehicles with real URLs
- ✅ **URL Shortening Jobs**: Created and processed URL shortening jobs for all vehicles
- ✅ **Comprehensive Documentation**: Complete integration guide and deployment instructions

## Technical Debt
- [ ] Fix "Unknown error" in URL shortening job processing
- [ ] Improve error handling and logging across all services
- [ ] Add comprehensive monitoring and alerting
- [ ] Optimize database queries and indexing
- [ ] Implement proper retry mechanisms with exponential backoff
