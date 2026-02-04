# Admin Module

System administration including user management, roles/permissions, audit logging, environment configuration, and system settings.

## Data Model

| Model | Table | Key Fields | Purpose |
|-------|-------|------------|---------|
| `Admin_User` | users | email, password, enabled, god_mode_enabled | Admin users |
| `Admin_Environment` | environments | slug, is_enabled, *_api_host | Environment configs |
| `Admin_AuditLog` | audit_log | user_id, action, status, module | Action tracking |
| `Admin_TelegramBot` | telegram_bots | token, handler, settings | Bot configurations |
| `Admin_Timezone` | timezones | name, country_code | Timezone reference |

### Spatie Permission Tables

| Table | Purpose |
|-------|---------|
| `roles` | Role definitions |
| `permissions` | Permission definitions |
| `model_has_roles` | User-role assignments |
| `role_has_permissions` | Role-permission mappings |

## Routes

**Prefix:** `/admin`
**Middleware:** `auth`

| Method | URI | Controller@Method | Purpose |
|--------|-----|-------------------|---------|
| GET | `/settings/users` | UsersController@main | User list |
| GET | `/settings/users/create` | UsersController@create | Create user |
| GET | `/settings/roles` | RolesController@main | Role management |
| GET | `/settings/permissions` | PermissionsController@main | Permission list |
| GET | `/audit-log` | AuditLogController@main | Audit log view |
| GET | `/event-log` | EventLogController@main | Event/error log |
| GET | `/settings/environments` | SettingsController@environments | Environment config |
| GET | `/system/cache` | SystemController@cache | Cache management |
| GET | `/telegram-bots` | TelegramBotController@main | Bot management |

## Key Logic

### Authentication

**File:** `app/Models/Admin/Admin_User.php`

User model extends Laravel's Authenticatable:
- Session-based auth via `web` guard
- Password expiry tracking (`password_last_changed_at`)
- Failed login counting
- Timezone preference

### God Mode

**File:** `app/Models/Admin/Admin_User.php:80-95`

Special elevated access:
```php
isGodModeEnabled(): bool
getHardcodedGodModeUserIds(): array // Returns [1, 2, 7, 10, 11]
```

Automatically enabled in non-production environments via `SharedVariables` middleware.

### Permission System (BROKEN)

**Status:** Permission middleware disabled with TODO

**File:** `app/Http/Middleware/PermissionMiddleware.php`

Current state: `return $next($request)` - passes all requests through.

Spatie permissions exist in database but not enforced on routes.

### Audit Logging

**Files:**
- `app/Events/Wallet/AuditLogEvent.php`
- `app/Listeners/AuditLogListener.php`

Event-driven logging:
1. Controller fires `AuditLogEvent` with before/after data
2. `AuditLogListener` creates `Admin_AuditLog` record
3. Tracks: user_id, action, ip_address, status, module, data changes

### Environment Management

**File:** `app/Models/Admin/Admin_Environment.php`

Multi-environment support:
- Each environment has its own database connections
- API endpoints configured per environment
- Routes use `{environmentSlug}` parameter
- `Admin_EnvironmentDatabaseConnection` stores connection details

### Telegram Integration

**Files:**
- `app/Services/Telegram/TelegramService.php`
- `app/Services/Telegram/TelegramBotService.php`
- `app/Services/Telegram/Handlers/`

Bot handlers:
- `TelegramSupportBotHandler` - Support ticket handling
- `TelegramSystemNotificationBotHandler` - System alerts

## Services

| Service | Purpose | File |
|---------|---------|------|
| PermissionsService | Permission checking (broken) | `app/Services/PermissionsService/` |
| NotificationService | User notifications | `app/Services/Notification/` |
| TelegramService | Bot API communication | `app/Services/Telegram/` |
| EnvironmentService | Environment switching | `app/Services/Environment/` |

## Middleware

| Middleware | Purpose | File |
|------------|---------|------|
| Authenticate | Session auth redirect | `app/Http/Middleware/Authenticate.php` |
| ApiGuard | IP + API key validation | `app/Http/Middleware/ApiGuard.php` |
| SharedVariables | Inject user/env context | `app/Http/Middleware/SharedVariables.php` |
| PermissionMiddleware | Permission check (disabled) | `app/Http/Middleware/PermissionMiddleware.php` |

## Configuration

| Env Variable | Default | Purpose |
|--------------|---------|---------|
| SESSION_LIFETIME | 120 | Session duration (minutes) |
| USER_PASSWORD_LIFETIME | - | Password expiry period |
| ADMIN_SKIN_CLASS | - | UI theme class |

## Known Issues

1. **Permission middleware disabled** - Routes not protected by roles
2. **God mode hardcoded** - Specific user IDs have elevated access
3. **No policies/gates** - Laravel authorization not implemented

## Related Modules

- [Wallet](wallet.md) - Entity management
- [Shared](shared.md) - Infrastructure management
