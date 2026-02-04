# Architecture Overview

## System Overview

Admin is a Laravel 8 PHP administrative backend for a gaming/gambling platform. It serves as the central control panel for managing multiple subsystems: wallet operations, game servers, backoffice operations, and infrastructure.

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Admin Panel (This App)                       │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────┐  ┌──────────┐  ┌───────────┐  ┌────────┐  ┌────────┐  │
│  │  Admin  │  │  Wallet  │  │ Gameserver│  │Backoff │  │ Shared │  │
│  │ Module  │  │  Module  │  │  Module   │  │ Module │  │ Module │  │
│  └────┬────┘  └────┬─────┘  └─────┬─────┘  └───┬────┘  └───┬────┘  │
│       │            │              │            │            │       │
│       └────────────┴──────┬───────┴────────────┴────────────┘       │
│                           │                                          │
│                    ┌──────┴──────┐                                   │
│                    │   Services  │                                   │
│                    └──────┬──────┘                                   │
└───────────────────────────┼─────────────────────────────────────────┘
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
┌─────────────────┐ ┌───────────────┐ ┌─────────────────┐
│   Wallet API    │ │ Gameserver API│ │ Backoffice API  │
│   (External)    │ │  (External)   │ │   (External)    │
└────────┬────────┘ └───────┬───────┘ └────────┬────────┘
         │                  │                  │
         └──────────────────┼──────────────────┘
                            │
                 ┌──────────┴──────────┐
                 │  Multiple Databases │
                 │  (per environment)  │
                 └─────────────────────┘
```

## Module Dependency Map

```
Admin ◄───────────────────┐
  │                       │
  ├──► Wallet ◄──────────►│ Gameserver
  │      │                │     │
  │      ▼                │     │
  │   EntityGroup ◄───────┴─────┘
  │      │
  │      ▼
  │   Backoffice ◄─► Entity
  │
  └──► Shared (Infrastructure)
```

## Request Lifecycle

```
Browser Request
      │
      ▼
┌─────────────────┐
│  public/index.php  │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────────┐
│              Global HTTP Middleware             │
│  TrustProxies → ValidatePostSize → TrimStrings │
│  → ConvertEmptyStringsToNull → LogSlowRequests │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│              Web Middleware Group               │
│  MaintenanceMode → EncryptCookies → Session    │
│  → VerifyCsrfToken → EnvironmentDbConnections  │
│  → ValidateConfig → SharedVariables            │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│              Route Middleware                   │
│  auth → permission (DISABLED) → user_can_use_  │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│                  Controller                      │
│  Thin layer - delegates to Services             │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│                   Service                        │
│  Business logic, external API calls             │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│                    Model                         │
│  Eloquent ORM with multi-DB connections         │
└────────────────────┬────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────┐
│                  Database                        │
│  Environment-specific connection routing        │
└─────────────────────────────────────────────────┘
```

## Authentication Flow

```
┌──────────────────────────────────────────────────────────────┐
│                     Authentication Flow                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Login Request                                               │
│       │                                                      │
│       ▼                                                      │
│  ┌─────────────────┐                                         │
│  │ LoginController │                                         │
│  │  authenticate() │                                         │
│  └────────┬────────┘                                         │
│           │                                                  │
│           ▼                                                  │
│  ┌─────────────────┐     ┌──────────────────┐               │
│  │ Auth::attempt() │────►│ Admin_User Model │               │
│  └────────┬────────┘     └──────────────────┘               │
│           │                                                  │
│           ▼                                                  │
│  ┌─────────────────┐                                         │
│  │ Session Created │                                         │
│  │ (Redis/File)    │                                         │
│  └────────┬────────┘                                         │
│           │                                                  │
│           ▼                                                  │
│  ┌─────────────────┐                                         │
│  │ SharedVariables │ ◄── Injects user, timezone, env        │
│  │   Middleware    │                                         │
│  └────────┬────────┘                                         │
│           │                                                  │
│           ▼                                                  │
│  ┌─────────────────┐                                         │
│  │ Check god_mode  │ ◄── Hardcoded IDs: 1,2,7,10,11         │
│  │ in config       │     OR non-prod environment            │
│  └─────────────────┘                                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

## Background Processing

```
┌─────────────────────────────────────────────────────────────┐
│                  Background Processing                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Scheduled Commands                      │    │
│  │  (app/Console/Kernel.php)                           │    │
│  │                                                     │    │
│  │  ┌─────────────────────────────────────────────┐   │    │
│  │  │ aggregation:data-sanity-data │ Every Minute │   │    │
│  │  └─────────────────────────────────────────────┘   │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Manual Commands (31 total)              │    │
│  │                                                     │    │
│  │  Aggregation: AggregateBusinessReport              │    │
│  │               AggregateDailyKpiPerWallet           │    │
│  │                                                     │    │
│  │  Archive:     ArchiveRounds, ArchiveSessions       │    │
│  │               ArchiveTransactionsOp31Table         │    │
│  │                                                     │    │
│  │  Maintenance: GarbageCollection (gc collect/       │    │
│  │               process-errors/process-cancels/      │    │
│  │               process-win-errors)                   │    │
│  │                                                     │    │
│  │  Monitoring:  ZabbixFetchData, SendNotifications   │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Event System                           │    │
│  │                                                     │    │
│  │  AuditLogEvent ──────► AuditLogListener            │    │
│  │       │                      │                      │    │
│  │       │                      ▼                      │    │
│  │       │               Admin_AuditLog                │    │
│  │       │                                             │    │
│  │       └── Fired on: created, updated, deleted      │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Notification Channels                  │    │
│  │                                                     │    │
│  │  MattermostNotification ──► Mattermost Webhook     │    │
│  │  EntityLowBalanceAlert ───► Telegram Bot           │    │
│  │  UserNotification ────────► Database (in-app)      │    │
│  │  EntityUsageLimit ────────► Email (queued)         │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## External Integration Points

```
┌─────────────────────────────────────────────────────────────┐
│                 External Integrations                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │  Wallet API │    │ Gameserver  │    │  Backoffice │     │
│  │             │    │     API     │    │     API     │     │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘     │
│         │                  │                  │             │
│         └──────────────────┼──────────────────┘             │
│                            │                                │
│                            ▼                                │
│                   ┌─────────────────┐                       │
│                   │ Environment-    │                       │
│                   │ specific URLs   │                       │
│                   │ and API keys    │                       │
│                   └─────────────────┘                       │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              Third-Party Services                   │    │
│  │                                                     │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │    │
│  │  │ Telegram │  │ Mattermost │  │CloudFlare│         │    │
│  │  │   Bots   │  │  Webhooks │  │   DNS    │         │    │
│  │  └──────────┘  └──────────┘  └──────────┘         │    │
│  │                                                     │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │    │
│  │  │  Zabbix  │  │  AWS S3  │  │  Fixer   │         │    │
│  │  │ Monitor  │  │ Storage  │  │ Exchange │         │    │
│  │  └──────────┘  └──────────┘  └──────────┘         │    │
│  │                                                     │    │
│  │  ┌──────────────────────────────────────────┐     │    │
│  │  │         100+ Game Providers              │     │    │
│  │  │  (Evolution, Playson, EGT, etc.)         │     │    │
│  │  └──────────────────────────────────────────┘     │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Database Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                   Database Connections                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Per-Environment Dynamic Connections:                       │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  admin-master        │ Admin users, audit, settings │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  wallet-master       │ Entities, games (write)      │    │
│  │  wallet-slave        │ Entities, games (read)       │    │
│  │  wallet-minor        │ Secondary operations         │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  gameserver-master   │ Sessions, rounds (write)     │    │
│  │  gameserver-slave    │ Sessions, rounds (read)      │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  backoffice-slave    │ Backoffice users (read)      │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  data-aggregation-*  │ KPI, analytics               │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  wallet-archive-*    │ Archived transactions        │    │
│  │  wallet-logs-*       │ Request/error logs           │    │
│  ├─────────────────────────────────────────────────────┤    │
│  │  joinplay-slave      │ Sandbox/JoinPlay data        │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  Connection Routing:                                        │
│  EnvironmentDbServersConnections middleware sets            │
│  connections based on {environmentSlug} in URL              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Key Architectural Patterns

### 1. Service Layer Pattern
Controllers are thin, delegating business logic to Services in `app/Services/`.

### 2. Multi-Environment Support
Routes include `{environmentSlug}` parameter, allowing same codebase to manage multiple environments with different database connections and API endpoints.

### 3. Worker Pattern
Background processing uses worker classes:
- `GarbageCollection/Workers/` - Data cleanup
- `Archive/Workers/` - Data archival
- `DataAggregation/` - KPI calculations

### 4. Event-Driven Auditing
Audit logging uses Laravel events/listeners for clean separation.

### 5. Domain-Organized Code
Models, controllers, views organized by domain (Admin, Wallet, Gameserver, Backoffice, Sandbox, Shared).

## Known Architectural Issues

1. **Permission system disabled** - Routes not protected by roles
2. **Hardcoded credentials** - CloudFlare API key in controller
3. **God mode hardcoded** - Specific user IDs have elevated access
4. **No API versioning** - API endpoints mixed with web routes
5. **Large config file** - `config/config.php` is 213KB with all GP configs
