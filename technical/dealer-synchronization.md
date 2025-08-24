# Dealer Synchronization Strategy

## Overview

This document outlines the strategy for keeping dealers synchronized between PayloadCMS (the admin interface) and Supabase (the data layer). This is critical for maintaining data consistency across the Open Dealer ecosystem.

## Architecture

### Data Flow
```
PayloadCMS (Admin) ←→ Supabase (Data Layer) ←→ Data API
```

### Components
- **PayloadCMS**: Admin interface for managing dealers
- **Supabase**: Primary data store for vehicles and dealer relationships
- **Data API**: Serves vehicle data using dealer relationships from Supabase

## Synchronization Strategy

### 1. Bidirectional Sync

#### PayloadCMS → Supabase (Primary)
- **Trigger**: Any dealer CRUD operation in PayloadCMS
- **Method**: Hooks in PayloadCMS dealer collection
- **Frequency**: Real-time (on every change)

#### Supabase → PayloadCMS (Read-only)
- **Purpose**: Data validation and consistency checks
- **Method**: Manual verification scripts
- **Frequency**: On-demand

### 2. Sync Triggers

#### Automatic Triggers
- **Dealer Creation**: New dealer added to PayloadCMS
- **Dealer Update**: Dealer information modified in PayloadCMS
- **Dealer Deletion**: Dealer removed from PayloadCMS
- **PayloadCMS Startup**: Initial sync of all dealers

#### Manual Triggers
- **Data Validation**: Verify consistency between systems
- **Bulk Operations**: Sync multiple dealers at once
- **Recovery**: Restore sync after system issues

## Implementation

### PayloadCMS Hooks

```typescript
// In od-cms/src/collections/Dealers.ts
hooks: {
  afterChange: [
    async ({ doc, operation }) => {
      await syncDealerToSupabase(doc, operation);
    },
  ],
  afterDelete: [
    async ({ doc }) => {
      await syncDealerToSupabase(doc, 'delete');
    },
  ],
}
```

### Sync Functions

#### Individual Dealer Sync
```typescript
async function syncDealerToSupabase(dealer: any, operation: 'create' | 'update' | 'delete') {
  if (operation === 'delete') {
    await supabase.from('dealers').delete().eq('id', dealer.id);
  } else {
    const dealerData = {
      id: dealer.id,
      name: dealer.name,
      slug: dealer.slug,
      domain: dealer.domain,
      rooftop_id: dealer.homenet_config?.rooftop_id || null,
      api_config: {
        platforms: dealer.platforms || [],
        homenet_config: dealer.homenet_config || {},
        // ... other configs
      },
      created_at: dealer.createdAt,
    };
    
    await supabase.from('dealers').upsert(dealerData, { onConflict: 'id' });
  }
}
```

#### Bulk Sync
```typescript
async function syncAllDealersToSupabase(payload: any) {
  const { docs: dealers } = await payload.find({
    collection: 'dealers',
    limit: 1000,
  });
  
  for (const dealer of dealers) {
    await syncDealerToSupabase(dealer, 'update');
  }
}
```

## Data Mapping

### PayloadCMS → Supabase

| PayloadCMS Field | Supabase Field | Notes |
|------------------|----------------|-------|
| `id` | `id` | UUID primary key |
| `name` | `name` | Dealer name |
| `slug` | `slug` | URL-friendly identifier |
| `domain` | `domain` | Website domain |
| `homenet_config.rooftop_id` | `rooftop_id` | HomeNet rooftop ID |
| `platforms` | `api_config.platforms` | Array of platform names |
| `homenet_config` | `api_config.homenet_config` | HomeNet configuration |
| `dealer_com_config` | `api_config.dealer_com_config` | Dealer.com configuration |
| `vinsolutions_config` | `api_config.vinsolutions_config` | VinSolutions configuration |
| `custom_config` | `api_config.custom_config` | Custom platform configuration |
| `utm_config` | `api_config.utm_config` | UTM tracking configuration |
| `createdAt` | `created_at` | Creation timestamp |

## Validation & Monitoring

### Consistency Checks

#### Manual Validation Script
```bash
# Run validation script
cd od-cms
doppler run -- node validate-dealer-sync.mjs
```

#### Automated Monitoring
- Log all sync operations
- Track sync failures
- Alert on data inconsistencies

### Error Handling

#### Sync Failures
1. **Retry Logic**: Automatic retry for transient failures
2. **Manual Recovery**: Manual sync scripts for persistent issues
3. **Alerting**: Notify administrators of sync failures

#### Data Conflicts
1. **Conflict Resolution**: PayloadCMS takes precedence
2. **Audit Trail**: Log all conflicts for review
3. **Manual Override**: Admin can force sync in either direction

## Usage

### Adding a New Dealer

1. **Create in PayloadCMS**: Use the admin interface
2. **Automatic Sync**: Dealer is automatically synced to Supabase
3. **Verification**: Check that dealer appears in Supabase

### Updating Dealer Information

1. **Edit in PayloadCMS**: Modify dealer details in admin
2. **Automatic Sync**: Changes are immediately synced to Supabase
3. **API Update**: Data API will use updated information

### Bulk Operations

```bash
# Sync all dealers manually
cd od-cms
doppler run -- node sync-dealers-manual.mjs

# Validate sync status
cd od-cms
doppler run -- node validate-dealer-sync.mjs
```

## Best Practices

### Data Integrity
- Always use PayloadCMS as the source of truth
- Never edit dealers directly in Supabase
- Use the sync scripts for bulk operations

### Performance
- Sync operations are asynchronous
- Bulk operations are batched
- Failed syncs are retried automatically

### Security
- Sync operations use service role credentials
- All operations are logged for audit
- Access to sync functions is restricted

## Troubleshooting

### Common Issues

#### Sync Not Working
1. Check environment variables
2. Verify Supabase connection
3. Check PayloadCMS logs

#### Data Inconsistencies
1. Run validation script
2. Check sync logs
3. Manual sync if needed

#### Performance Issues
1. Monitor sync frequency
2. Check for duplicate operations
3. Optimize bulk operations

### Recovery Procedures

#### Complete Sync Reset
```bash
# Clear Supabase dealers
# Re-sync from PayloadCMS
cd od-cms
doppler run -- node sync-dealers-manual.mjs
```

#### Individual Dealer Recovery
```bash
# Manual sync specific dealer
cd od-cms
doppler run -- node sync-dealer-specific.mjs --dealer-id=<ID>
```

## Future Enhancements

### Planned Improvements
1. **Real-time Sync**: WebSocket-based real-time synchronization
2. **Conflict Resolution UI**: Admin interface for resolving conflicts
3. **Sync Analytics**: Dashboard showing sync status and metrics
4. **Automated Testing**: Automated tests for sync functionality

### Monitoring & Alerting
1. **Sync Health Checks**: Automated health checks
2. **Alert Integration**: Integration with monitoring systems
3. **Performance Metrics**: Track sync performance over time

## Conclusion

This synchronization strategy ensures that dealer data remains consistent between PayloadCMS and Supabase, providing a reliable foundation for the Open Dealer ecosystem. The automatic sync mechanisms reduce manual intervention while the manual tools provide flexibility for bulk operations and recovery scenarios.
