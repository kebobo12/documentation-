# Discovery Report: Admin (timelesstech Gaming Platform)

**Scanned**: 2026-02-04
**Laravel**: 8
**PHP**: 8.1+
**Key Packages**: yajra/laravel-datatables-oracle, league/flysystem-aws-s3-v3, erusev/parsedown, spatie/laravel-permission

## Stats

| Metric | Count |
|--------|-------|
| Models | 86 |
| Controllers | 85 |
| Services | 69 |
| Migrations | 144 |
| Routes | 489 (487 web, 2 API) |
| Jobs | 0 |
| Events | 1 |
| Listeners | 1 |
| Console Commands | 31 |
| Notifications | 4 |
| Blade Templates | 328 |
| Environment Variables | 54 |

## Module Map

| Module/Domain | Models | Controllers | Routes | Priority | Notes |
|---------------|--------|-------------|--------|----------|-------|
| **Wallet** | 22 | 33 | 240 | 1 | Core financial system - entities, transactions, game providers, currencies |
| **Gameserver** | 41 | 21 | 116 | 2 | Game management - sessions, rounds, lobbies, multiplayer, raffles |
| **Admin** | 9 | 13 | 55 | 3 | System administration - users, roles, audit logs, settings |
| **Shared** | - | 10 | 33 | 4 | Infrastructure - servers, deployment, CloudFlare, SSL |
| **Sandbox/JoinPlay** | 6 | 6 | 20 | 5 | Testing environment - operators, HTTP auth users |
| **Backoffice** | 4 | 2 | 12 | 6 | Secondary operator portal - user management |
| **DataAggregation** | 4 | - | - | 7 | KPI reporting and metrics (read-only) |
| **Monitoring** | - | - | 3 | 8 | Syslog viewing |

## Key Model Relationships

### Wallet Domain (Core Business)
```
Wallet_Entity ──belongsToMany──► Wallet_EntityGroup (via entities_xref_entities_groups)
Wallet_Entity ──belongsToMany──► Wallet_Game (via entities_xref_disabled_games)
Wallet_EntityGroup ──belongsTo──► Wallet_Entity (master_entity_id)
Wallet_EntityGroup ──belongsTo──► Wallet_EntityGroup (reseller_group_id, self-referencing)
```

### Gameserver Domain
```
GS_GameTransaction ──hasMany──► GS_WalletTransaction
GS_GameTransaction ──hasOne──► GS_TransactionPendingWin
GS_Lobby ──hasMany──► GS_LobbyCategory
GS_LobbyCategory ──hasMany──► GS_LobbyCategoryGame
```

### Cross-Domain
```
Admin_User ──belongsTo──► Admin_Timezone
Backoffice_User ──belongsTo──► Wallet_EntityGroup (reseller_group_id)
JoinPlay_Transaction ──belongsTo──► JoinPlay_User
JoinPlay_Transaction ──belongsTo──► JoinPlay_Game
```

## Auth System

### Authentication
- **Primary Guard**: `web` (session-based)
- **User Model**: `App\Models\Admin\Admin_User`
- **API Guard**: Token-based + IP whitelist + X-Authorization header

### Authorization Status
| Component | Status |
|-----------|--------|
| Spatie Permission | Installed, CRUD works |
| Permission Middleware | **DISABLED** (TODO: broken) |
| Route Protection | Auth only, role checks commented out |
| Policies/Gates | Not implemented |
| God Mode | Hardcoded for user IDs 1, 2, 7, 10, 11 |

### Middleware Stack
- `auth` - Session authentication
- `apiguard` - API IP whitelist + key validation
- `permission` / `perm` - **BROKEN** (passes through)
- `user_can_use_override` - Game provider proxy permission
- `VerifyTelegramRequest` - Telegram webhook verification

## External Integrations

### Currency Exchange
| Provider | Status | Config |
|----------|--------|--------|
| Fixer (DataFixer) | Active | CURRENCY_RATES_API_KEY |
| XE | Available | XE_EXCHANGE_RATES_API_* |
| BlueLytics | Available | - |

### Communication
| Service | Purpose | Method |
|---------|---------|--------|
| Telegram | Bot webhooks, notifications | REST API via curl |
| Mattermost | Team notifications | Webhook |
| SMTP/Mailgun | Email | Laravel Mail |

### Infrastructure
| Service | Purpose |
|---------|---------|
| AWS S3 | Game thumbnails storage |
| Redis | Cache, sessions |
| CloudFlare | DNS/Zone management |
| Zabbix | Server monitoring |

### Internal APIs
| API | Purpose |
|-----|---------|
| Backoffice API | Business reports |
| Relayer Backoffice API | Secondary backoffice |
| Wallet API | Wallet operations |
| Gameserver API | Game state management |
| Hand History API | Game history data |

### Game Providers
- **100+ game provider configurations** in `config/config.php`
- Each provider has: api_url, api_key, hostname headers, custom endpoints
- Notable: Playson, EGT Digital, DreamPlay, OneTouch, Evolution (Trinity)

## Background Processing

### Scheduled Tasks
| Command | Frequency | Purpose |
|---------|-----------|---------|
| `aggregation:data-sanity-data` | Every minute | Dashboard sanity checks |

### Key Console Commands
| Category | Commands |
|----------|----------|
| Aggregation | AggregateBusinessReport, AggregateDailyKpiPerWallet, AggregateDataSanityData |
| Archive | ArchiveRounds, ArchiveSessions, ArchiveTransactionsOp31Table, ArchiveCampaigns |
| Maintenance | GarbageCollection (4 actions), EntityUsageLimitsAggregation, FetchCurrencyExchangeRates |
| Monitoring | ZabbixFetchData, SendNotifications |

### Event System
```
AuditLogEvent → AuditLogListener → Admin_AuditLog
```

## Frontend Stack

| Layer | Technology |
|-------|------------|
| Templates | Laravel Blade (328 files) |
| CSS Framework | Bootstrap 3.3.7 (migrating to Tailwind 3.2.4) |
| JavaScript | jQuery-based (no SPA framework) |
| Tables | DataTables with 7 extensions |
| Forms | Bootstrap Select, Select2, Multiselect |
| Charts | Chart.js |
| Maps | jVectorMap |
| Admin Panel | Custom-built (no Filament/Nova/Backpack) |

## Documentation Priority (Ranked)

### Tier 1 - Critical (Document First)
1. **Wallet Module** - Entity management, game providers, transactions, currencies
2. **Gameserver Module** - Games, sessions, rounds, lobbies
3. **Auth System** - Current state, broken permissions, workarounds

### Tier 2 - Important
4. **Admin Module** - User management, roles, audit logs
5. **Shared/Infrastructure** - Server management, deployment, monitoring

### Tier 3 - Supporting
6. **Sandbox Module** - Testing environment
7. **Backoffice Module** - Operator portal
8. **Background Jobs** - Aggregation, archiving, garbage collection

## Architecture Notes

### Multi-Environment Support
- Routes use `{environmentSlug}` parameter
- Database connections per environment
- Configuration loaded dynamically

### Database Connections
| Connection | Purpose |
|------------|---------|
| admin-master | Admin operations |
| wallet-master/slave/minor | Wallet system |
| gameserver-master/slave | Game data |
| backoffice-master | Entity management |
| data-aggregation-master | Analytics |
| wallet-archive-master | Archived data |
| wallet-logs-master | Logging |

### Service Layer Pattern
- Controllers are thin (delegate to services)
- 69 services organized by domain
- Worker pattern for background processing (GarbageCollection, Archive)
- No repository pattern (data access in models/services)

### Security Concerns Found
- CloudFlare API key hardcoded in controller
- Permission middleware disabled
- God mode hardcoded for specific user IDs
- SSL verification disabled in local environments
