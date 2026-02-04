# Wallet Module

Core financial system managing entities, transactions, game providers, currencies, and balance operations.

## Data Model

| Model | Table | Key Fields | Purpose |
|-------|-------|------------|---------|
| `Wallet_Entity` | entities | name, status, config, ggr_usage_limit | Casino operator clients |
| `Wallet_EntityGroup` | entities_groups | master_entity_id, reseller_group_id | Group operators by company |
| `Wallet_Game` | games | gameprovider_id, vendor, themes, features | Game catalog |
| `Wallet_Gameprovider` | gameproviders | name, enabled, api_url, timeout_seconds | Game provider configs |
| `Wallet_Currency` | currencies | code (PK), name, symbol | Supported currencies |

### Key Relationships

| From | Relation | To | Purpose |
|------|----------|-----|---------|
| Entity | belongsToMany | EntityGroup | Group membership |
| Entity | belongsToMany | Game | Disabled games per entity |
| EntityGroup | belongsTo | Entity | Master entity |
| EntityGroup | belongsTo | EntityGroup | Reseller hierarchy |
| GameproviderConfig | belongsTo | Entity | Per-entity GP overrides |

## Routes

**Prefix:** `/wallet/{environmentSlug}`
**Middleware:** `auth`, `permission:wallet_*`

| Method | URI | Controller@Method | Purpose |
|--------|-----|-------------------|---------|
| GET | `/entities` | EntitiesController@main | List entities |
| GET | `/entities/create` | EntitiesController@create | Create form |
| POST | `/entities/insert` | EntitiesController@insert | Store entity |
| GET | `/entities/{id}/edit` | EntitiesController@edit | Edit form |
| POST | `/entities/{id}/update` | EntitiesController@update | Update entity |
| GET | `/games` | GamesController@main | List games |
| GET | `/gameproviders` | GameprovidersController@main | List providers |
| GET | `/transactions` | TransactionsController@main | Transaction log |
| GET | `/rounds` | RoundsController@main | Game rounds |
| GET | `/error-log` | ErrorLogController@main | Error tracking |

## Key Logic

### Entity Balance Management

**File:** `app/Models/Wallet/Wallet_Entity.php:200-280`

Entities track GGR usage limits and balance:

```
isUsageLimitExceeded() → Check if GGR limit reached
getLimitPercentage() → Calculate usage %
getAvailableBalance($currency) → Get remaining balance with conversion
getUnusedBalance($currency) → Get unused portion
```

**Flow:** Entity balance changes → DataAggregation aggregates → Dashboard displays

### Game Disabling

**File:** `app/Http/Controllers/Wallet/EntitiesController.php`

Entities can disable specific games:
1. `disabledGames()` relationship loads disabled games
2. UI shows toggle per game
3. Stored in `entities_xref_disabled_games` pivot

### Game Provider Configuration

**File:** `app/Models/Wallet/Wallet_GameproviderConfig.php`

Per-entity game provider overrides:
- API credentials
- Currency mappings
- Timeout settings
- Custom parameters

### Trinity Proxy System

**Files:** `app/Models/Wallet/Wallet_GameProviderProxy*.php`

Evolution gaming integration via proxy servers:
- `GameProviderProxyServer` - Main proxy config
- `GameProviderProxyOverride` - Entity-specific overrides
- `GameProviderProxyRedisServer` - Redis session storage
- `GameProviderProxyDBReplica` - Database replicas
- `GameProviderProxyApiServer` - API server routing

## Services

| Service | Purpose | File |
|---------|---------|------|
| WalletService | Wallet API calls | `app/Services/Wallet/WalletService.php` |
| TrinityProxyService | Evolution proxy management | `app/Services/TrinityProxy/TrinityProxyService.php` |
| EntityBalanceLimitationService | Balance limit calculations | `app/Services/EntityUsageLimitsAggregation/` |

## Configuration

| Env Variable | Default | Purpose |
|--------------|---------|---------|
| WALLET_API_HOST | - | Wallet service URL |
| WALLET_API_KEY | - | Wallet API key |
| WALLET_API_DATA_CACHE_TTL | 300 | Cache duration (seconds) |

## Related Modules

- [Gameserver](gameserver.md) - Game session management
- [Admin](admin.md) - User permissions for wallet access
