# API Endpoints

**Total Endpoints:** 34 across 6 domains

This application uses a hybrid approach where API endpoints are mixed with web routes under environment-specific prefixes.

## Admin API

### Process Control

**`GET /admin/api/process-control`**

**Auth:** None (internal)
**Controller:** `Admin\ApiController@processControl`

Controls background processes (rounds archiving, garbage collection) for deployment and process management.

### Balance Operations

**`POST /admin/api/set-balance-limit`**

**Auth:** `apiguard` (IP whitelist + API key)
**Controller:** `Admin\ApiController@setBalanceLimit`

Set balance limitation for wallet entities.

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| entity_id | int | Yes | Target entity |
| amount | decimal | Yes | Limit amount |
| currency | string | Yes | Currency code |
| type | string | Yes | Limit type |

---

**`POST /admin/api/move-balance-limit`**

**Auth:** `apiguard`
**Controller:** `Admin\ApiController@moveBalanceLimit`

Transfer balance limit from one entity to another.

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| from_entity_id | int | Yes | Source entity |
| to_entity_id | int | Yes | Target entity |
| amount | decimal | Yes | Amount to transfer |

---

**`POST /admin/api/enable-disable-game`**

**Auth:** `apiguard`
**Controller:** `Admin\ApiController@enableDisableGame`

Enable or disable specific games for wallet entities.

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| entity_id | int | Yes | Target entity |
| game_id | int | Yes | Game to toggle |
| enabled | bool | Yes | Enable/disable flag |

### Notifications & Webhooks

**`ANY /admin/alert/mattermost`**

**Auth:** None (webhook)
**Controller:** `Admin\NotificationController@alertMattermost`

Receive Mattermost alert notifications.

---

**`ANY /admin/alert/admin`**

**Auth:** None (webhook)
**Controller:** `Admin\NotificationController@notifyAdmin`

Send admin notifications.

---

**`POST /api/telegram/webhook/{botExternalId}`**

**Auth:** `VerifyTelegramRequest`
**Controller:** `Admin\TelegramApiController@webhook`

Telegram bot webhook endpoint for receiving updates.

| Param | Location | Type | Required |
|-------|----------|------|----------|
| botExternalId | path | string | Yes |
| update | body | json | Yes |

### System & Data

**`GET /admin/audit-log/data`**

**Auth:** `auth`
**Controller:** `Admin\AuditLogController@auditLogData`

Fetch audit log data in JSON format for DataTables.

---

**`POST /admin/system/cache/flush`**

**Auth:** `auth`
**Controller:** `Admin\SystemController@flushCache`

Clear system cache.

---

## Wallet API

### Data Endpoints

**`GET /wallet/{environmentSlug}/currencies/data`**

**Auth:** `auth`
**Controller:** `Wallet\CurrenciesController@data`

Fetch list of wallet currencies.

---

**`POST /wallet/{environmentSlug}/error-log/error/data`**

**Auth:** `auth`
**Controller:** `Wallet\ErrorLogController@errorsData`

Retrieve paginated error log data.

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| start | int | No | Pagination offset |
| length | int | No | Page size |
| search | string | No | Search filter |

---

**`GET|POST /wallet/{environmentSlug}/request-log/data`**

**Auth:** `auth`
**Controller:** `Wallet\RequestLogController@data`

Fetch request log entries with pagination and filtering.

---

**`GET /wallet/{environmentSlug}/games/data`**

**Auth:** `auth`
**Controller:** `Wallet\GamesController@data`

Get paginated games list with metadata.

### System API

**`POST /wallet/{environmentSlug}/system/api`**

**Auth:** `auth`
**Controller:** `Wallet\SystemController@api`

Generic wallet system API endpoint for internal wallet service calls.

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| action | string | Yes | API action to execute |
| params | object | No | Action parameters |

---

## Gameserver API

### Data Endpoints

**`GET /gameserver/{environmentSlug}/game-simulator/data`**

**Auth:** `auth`
**Controller:** `Gameserver\GameSimulatorController@data`

Retrieve game simulation data.

---

**`POST /gameserver/{environmentSlug}/error-log/data`**

**Auth:** `auth`
**Controller:** `Gameserver\ErrorLogController@errorsData`

Fetch gameserver error log entries.

---

**`GET /gameserver/{environmentSlug}/games/data`**

**Auth:** `auth`
**Controller:** `Gameserver\GamesController@data`

Get gameserver games list.

---

**`GET|POST /gameserver/{environmentSlug}/game-transactions/data`**

**Auth:** `auth`
**Controller:** `Gameserver\GameTransactionController@data`

Retrieve game transaction data.

---

**`GET|POST /gameserver/{environmentSlug}/wallet-transactions/data`**

**Auth:** `auth`
**Controller:** `Gameserver\WalletTransactionController@data`

Fetch wallet transaction data from gameserver.

### System API

**`POST /gameserver/{environmentSlug}/system/api-call`**

**Auth:** `auth`
**Controller:** `Gameserver\SystemController@gameServerApiCall`

Generic gameserver API endpoint for system operations.

---

## Backoffice API

**`GET /backoffice/{environmentSlug}/users/data`**

**Auth:** `auth`
**Controller:** `Backoffice\UsersController@usersData`

Fetch backoffice users list.

---

**`POST /backoffice/{environmentSlug}/system/api-call`**

**Auth:** `auth`
**Controller:** `Backoffice\SystemController@backofficeApiCall`

Generic backoffice API endpoint.

---

## Shared/Monitoring API

### Syslog

**`POST /monitoring/{environmentSlug}/syslog/data`**

**Auth:** `auth`
**Controller:** `Shared\SyslogController@syslogData`

Retrieve syslog data for monitoring.

---

**`POST /shared/syslog/data`**

**Auth:** `auth`
**Controller:** `Shared\SyslogController@syslogData`

Fetch syslog data (shared section).

### Database Management

**`POST /shared/databases/flush-status`**

**Auth:** `auth`
**Controller:** `Shared\DatabasesController@flushDbServerStatus`

Flush database server status cache.

### Server Management (Zabbix)

**`GET /shared/servers/zabbix/fetch/host-ids`**

**Auth:** `auth`
**Controller:** `Shared\ServersController@fetchZabbixHostIds`

Fetch Zabbix monitoring host IDs.

---

**`GET /shared/servers/zabbix/fetch/data`**

**Auth:** `auth`
**Controller:** `Shared\ServersController@fetchZabbixData`

Retrieve Zabbix monitoring data.

---

**`GET /shared/servers/zabbix/fetch/synch-hosts`**

**Auth:** `auth`
**Controller:** `Shared\ServersController@syncZabbixHosts`

Synchronize Zabbix hosts with local database.

### CloudFlare Integration

**`GET /shared/cf/fetch`**

**Auth:** `auth`
**Controller:** `Shared\CloudFlareController@fetchCloudFlareData`

Fetch CloudFlare DNS and zone data.

---

## Sandbox/JoinPlay API

**`GET /sandbox/{environmentSlug}/transactions/data`**

**Auth:** `auth`
**Controller:** `Sandbox\TransactionsController@transactionsData`

Retrieve sandbox transaction data.

---

**`POST /sandbox/{environmentSlug}/system/api-call`**

**Auth:** `auth`
**Controller:** `Sandbox\SystemController@joinPlayApiCall`

Generic sandbox/joinplay API endpoint.

---

## Authentication Methods

| Method | Usage | Description |
|--------|-------|-------------|
| `auth` | Most endpoints | Session-based authentication |
| `apiguard` | Admin balance/game APIs | IP whitelist + X-Authorization header |
| `VerifyTelegramRequest` | Telegram webhook | Telegram signature verification |
| None | Alert webhooks | Public endpoints for external services |

## API Guard Details

The `apiguard` middleware (`app/Http/Middleware/ApiGuard.php`) validates:

1. **IP Whitelist:**
   - Production: 51.255.102.229, 15.235.143.223, 45.63.121.138
   - Staging: 46.105.248.100

2. **X-Authorization Header:** API key verified with `password_verify()`

## Error Responses

| Code | Description |
|------|-------------|
| 401 | Unauthenticated |
| 403 | Forbidden (IP not whitelisted or invalid API key) |
| 404 | Resource not found |
| 422 | Validation failed |
| 500 | Server error |

## Endpoint Summary by Domain

| Domain | Count | Pattern |
|--------|-------|---------|
| Admin API | 12 | `/admin/api/*`, `/admin/*` |
| Wallet API | 5 | `/wallet/{env}/*` |
| Gameserver API | 6 | `/gameserver/{env}/*` |
| Backoffice API | 2 | `/backoffice/{env}/*` |
| Shared/Monitoring | 7 | `/shared/*`, `/monitoring/{env}/*` |
| Sandbox/JoinPlay | 2 | `/sandbox/{env}/*` |
