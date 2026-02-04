# Database Schema

**Total Migrations:** 144 | **Total Tables:** 40+

## Database Connections

| Connection | Purpose | Read/Write |
|------------|---------|------------|
| `admin-master` | Admin panel, users, permissions, audit | R/W |
| `wallet-master` | Entities, games, transactions | Write |
| `wallet-slave` | Entities, games, transactions | Read |
| `wallet-minor` | Secondary wallet operations | R/W |
| `gameserver-master` | Game server data | Write |
| `gameserver-slave` | Game server data | Read |
| `backoffice-slave` | Backoffice users | Read |
| `data-aggregation-master` | KPI & analytics | Write |
| `data-aggregation-slave` | KPI & analytics | Read |
| `wallet-archive-master` | Archived transaction data | R/W |
| `wallet-logs-master` | Request/error logs | R/W |
| `joinplay-slave` | JoinPlay/sandbox data | Read |

---

## Admin Domain

### users
**Model:** `Admin_User`

| Column | Type | Nullable | Default | Index |
|--------|------|----------|---------|-------|
| id | unsigned int | - | AUTO_INCREMENT | PK |
| name | string | - | - | - |
| email | string | - | - | UNIQUE |
| password | string | - | - | - |
| remember_token | string(100) | YES | - | - |
| enabled | tinyint | YES | - | - |
| password_last_changed_at | timestamp | YES | - | - |
| timezone_id | int | YES | - | FK |
| failed_login_count | int | YES | - | - |
| last_active_at | timestamp | YES | - | - |
| god_mode_enabled | tinyint | YES | - | - |
| gameprovider_proxy_manipulation | tinyint | YES | - | - |
| is_developer | tinyint | YES | - | - |
| telegram_user_id | varchar(50) | YES | - | - |
| created_at | timestamp | - | - | - |
| updated_at | timestamp | - | - | - |

**Foreign Keys:** `timezone_id → timezones.id`

### permissions
**Model:** Spatie Permission

| Column | Type | Nullable | Default | Index |
|--------|------|----------|---------|-------|
| id | unsigned int | - | AUTO_INCREMENT | PK |
| name | string | - | - | UNIQUE |
| guard_name | string | - | - | - |
| created_at | timestamp | - | - | - |
| updated_at | timestamp | - | - | - |

### roles
**Model:** Spatie Role

| Column | Type | Nullable | Default | Index |
|--------|------|----------|---------|-------|
| id | unsigned int | - | AUTO_INCREMENT | PK |
| name | string | - | - | - |
| guard_name | string | - | - | - |
| created_at | timestamp | - | - | - |
| updated_at | timestamp | - | - | - |

### model_has_roles
| Column | Type | Index |
|--------|------|-------|
| role_id | unsigned int | FK → roles.id |
| model_type | string | - |
| model_id | unsigned bigint | - |

**Primary Key:** (role_id, model_id, model_type)

### role_has_permissions
| Column | Type | Index |
|--------|------|-------|
| permission_id | unsigned int | FK → permissions.id |
| role_id | unsigned int | FK → roles.id |

**Primary Key:** (permission_id, role_id)

### audit_log
**Model:** `Admin_AuditLog`

| Column | Type | Nullable | Default |
|--------|------|----------|---------|
| id | unsigned int | - | AUTO_INCREMENT |
| user_id | int unsigned | YES | - |
| action | string | - | - |
| ip_address | string | - | - |
| status | enum('SUCCESS', 'FAILURE') | - | - |
| module | string | - | - |
| before_action_data | text | YES | - |
| after_action_data | text | YES | - |
| created_at | timestamp | - | - |
| updated_at | timestamp | - | - |

### event_log

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | bigint unsigned | - | PK |
| uri | text | YES | - |
| request | text | YES | - |
| type | enum('ERROR', 'WARNING', 'NOTICE', 'INFO', 'DEBUG') | - | - |
| message | varchar(255) | - | INDEX |
| details | mediumtext | YES | - |
| exception_code | smallint unsigned | YES | - |
| exception_class | varchar(50) | YES | INDEX |
| exception_message | text | YES | INDEX |
| user_id | int unsigned | YES | - |
| unique_request_id | char(20) | - | INDEX |
| ip_address | varchar(45) | - | - |
| timestamp | datetime(3) | - | INDEX |
| is_acknowledged | tinyint unsigned | - | INDEX |
| acknowledged_by_admin_id | smallint unsigned | YES | - |
| acknowledged_at | datetime | YES | - |

### timezones

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | unsigned int | - | PK |
| name | varchar(35) | - | UNIQUE |
| country_code | char(2) | - | INDEX |

### environments
**Model:** `Admin_Environment`

| Column | Type | Nullable |
|--------|------|----------|
| id | unsigned int | - |
| name | varchar(100) | - |
| slug | string | - |
| is_enabled | tinyint | - |
| description | varchar(400) | YES |
| wallet_api_host | varchar(400) | YES |
| wallet_api_key | varchar(100) | YES |
| gameserver_api_host | varchar(400) | YES |
| gameserver_api_key | varchar(100) | YES |
| bo_api_host | varchar(400) | YES |
| historyservice_api_host | varchar(400) | YES |
| historyservice_api_key | varchar(100) | YES |

### environments_database_connections
**Model:** `Admin_EnvironmentDatabaseConnection`

| Column | Type | Nullable | Default |
|--------|------|----------|---------|
| id | unsigned int | - | AUTO_INCREMENT |
| environment_id | tinyint unsigned | - | - |
| connection_name | varchar(100) | - | - |
| driver | varchar(15) | - | 'mysql' |
| database | varchar(100) | - | - |
| host | varchar(150) | - | - |
| port | varchar(10) | - | '3306' |
| username | string | - | - |
| password | varchar(300) | YES | - |
| server_timezone | varchar(100) | - | 'UTC' |
| option_pdo_emulate_prepares | enum('true','false') | - | 'false' |
| is_enabled | tinyint(1) | YES | 1 |

### telegram_bots

| Column | Type | Nullable |
|--------|------|----------|
| id | int | - |
| name | varchar(100) | YES |
| environment_slug | varchar(50) | YES |
| handler | varchar(50) | YES |
| enabled | tinyint | YES |
| webhook_url | varchar(100) | YES |
| webhook_secret_token | varchar(100) | YES |
| token | varchar(100) | YES |
| settings | longtext | YES |
| external_id | varchar(100) | YES |

### telegram_bots_entities

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | int | - | PK |
| telegram_bot_id | int | YES | INDEX, FK |
| entity_id | int | YES | INDEX |
| chat_id | varchar(100) | YES | INDEX |
| chat_title | varchar(255) | YES | - |
| created_at | timestamp | YES | - |
| updated_at | timestamp | YES | - |

---

## Infrastructure Domain

### servers

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | unsigned int | - | PK |
| hostname | varchar(65) | - | INDEX |
| zabbix_host_id | int unsigned | YES | - |
| ip_external | varchar(45) | YES | INDEX |
| ip_internal | varchar(45) | YES | INDEX |
| region | enum('EU', 'AS', 'AF', 'SA', 'AU') | - | INDEX |
| environment | enum('production', 'beta', 'staging', 'dev', 'local') | YES | - |
| is_behind_cf | tinyint unsigned | - | - |
| category | enum('wallet_api', 'wallet_launcher', 'redis', 'db', 'haproxy', 'apps', etc.) | YES | - |
| comment | mediumtext | YES | - |

### cf_zones

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | varchar(50) | - | PK |
| name | varchar(100) | YES | - |
| status | varchar(50) | YES | - |
| type | varchar(50) | YES | - |
| plan | varchar(50) | YES | - |
| name_servers | tinytext | YES | - |

### cf_zones_dns_records

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | varchar(50) | - | PK |
| zone_id | varchar(50) | - | FK → cf_zones |
| zone_name | varchar(255) | - | - |
| type | varchar(20) | YES | - |
| name | varchar(100) | YES | - |
| content | varchar(100) | YES | - |
| proxiable | tinyint | YES | - |
| proxied | tinyint | YES | - |
| ttl | smallint | YES | - |

### ansible_log

| Column | Type | Nullable |
|--------|------|----------|
| id | unsigned int | - |
| playbook | varchar(100) | - |
| roles | text | - |
| region | varchar(2) | - |
| inventory | varchar(100) | - |
| target | varchar(100) | - |
| recap_ok | smallint unsigned | - |
| recap_changed | smallint unsigned | - |
| recap_unreachable | smallint unsigned | - |
| recap_failed | smallint unsigned | - |
| time_elapsed_seconds | int unsigned | - |
| timestamp | datetime(3) | - |
| output | mediumtext | YES |

---

## Data Aggregation Domain

### kpi_per_wallet
**Model:** `DataAggregation_KpiPerEntity` (via period join)

| Column | Type | Index |
|--------|------|-------|
| date | date | PK (composite) |
| entity_id | smallint unsigned | PK |
| wallet_id | bigint | PK, INDEX |
| game_id | bigint | PK |
| gameprovider_id | smallint unsigned | PK |
| vendor | varchar(50) | PK |
| currency_code | char(4) | PK |
| bet_amount | decimal(18,2) unsigned | - |
| bet_amount_in_eur | decimal(18,2) unsigned | - |
| bet_count | int unsigned | - |
| win_amount | decimal(18,2) unsigned | - |
| win_amount_in_eur | decimal(18,2) unsigned | - |
| win_count | int unsigned | - |
| rake_amount | decimal(18,4) unsigned | - |
| gross_gaming_revenue | decimal(18,2) | - |
| gross_gaming_revenue_in_eur | decimal(18,2) | - |
| freespin_amount | decimal(18,2) unsigned | - |
| freespin_count | int unsigned | - |
| jackpot_amount | decimal(18,2) unsigned | - |
| jackpot_count | int unsigned | - |
| transactions_count | bigint unsigned | - |
| side_bets_count | int unsigned | - |
| side_bets_ggr | decimal(18,2) | - |
| side_bets_ggr_in_eur | decimal(18,2) | - |
| timezone | varchar(35) | - |
| is_dirty | tinyint unsigned | - |

### limits_per_entity
**Model:** `DataAggregation_LimitsPerEntity`

| Column | Type | Index |
|--------|------|-------|
| entity_id | smallint unsigned | PK |
| period | varchar(16) | PK |
| total_bet | decimal(18,4) unsigned | - |
| total_win | decimal(18,4) unsigned | - |
| total_net | decimal(18,4) | - |
| total_cost | decimal(18,2) | - |
| negative_vendors | text | - |

### campaigns_kpi_per_entity

| Column | Type | Index |
|--------|------|-------|
| date | date | PK (composite) |
| entity_id | smallint unsigned | PK |
| campaign_id | int unsigned | PK |
| campaign_code | varchar(100) | PK |
| gameprovider_id | smallint unsigned | PK |
| vendor | varchar(50) | PK |
| currency_code | char(4) | PK |
| win_amount | decimal(18,2) unsigned | - |
| win_count | int unsigned | - |
| transactions_count | bigint unsigned | - |
| active_wallets_count | bigint unsigned | - |
| timezone | varchar(35) | - |

---

## Archive Domain

### archive_table_of_contents
**Connection:** `wallet-archive-master`

| Column | Type | Nullable | Default |
|--------|------|----------|---------|
| table_name | varchar(80) | - | PK |
| table_type | enum('ROUNDS', 'TRANSACTIONS', 'SESSIONS', etc.) | - | - |
| id_from | bigint unsigned | YES | - |
| id_to | bigint unsigned | YES | - |
| round_id_from | bigint | YES | - |
| round_id_to | bigint | YES | - |
| created_at_from | datetime(3) | YES | - |
| created_at_to | datetime(3) | YES | - |
| rows_count | bigint unsigned | YES | - |
| row_format | enum('DYNAMIC', 'COMPRESSED', 'COMPACT', 'FIXED') | YES | - |
| data_size | int unsigned | YES | - |
| index_size | int unsigned | YES | - |
| is_complete | tinyint unsigned | - | 0 |
| is_backed_up | tinyint unsigned | - | 0 |
| is_dropped | tinyint unsigned | - | - |
| updated_at | datetime(3) | YES | - |

---

## Garbage Collection Domain

### gc_rounds_flagged

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| round_id | bigint | - | PK |
| gameprovider_id | smallint | - | INDEX |
| reason | text | - | - |

### gc_transactions_flagged

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| transaction_id | bigint | - | PK |
| remote_transaction_id | varchar(60) | YES | - |
| gameprovider_id | smallint | YES | - |
| reason | text | YES | - |

### gc_broken_splayers

| Column | Type | Index |
|--------|------|-------|
| id | bigint unsigned | PK |
| gameprovider_id | smallint unsigned | INDEX |
| wallet_id | bigint unsigned | INDEX |
| splayer_id | bigint unsigned | - |
| splayer_username | varchar(100) | - |
| reason | text | - |
| updated_at | datetime(3) | - |

### gc_gp_cleared_pendings

| Column | Type | Nullable |
|--------|------|----------|
| gameprovider_id | smallint | PK |
| cleared_till | datetime(3) | YES |

---

## Notifications Domain

### notifications

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | unsigned int | - | PK |
| type | varchar(255) | - | - |
| notifiable_type | varchar(255) | - | INDEX |
| notifiable_id | bigint unsigned | - | INDEX |
| hash | char(40) | - | INDEX |
| data | text | - | - |
| read_at | datetime(3) | YES | - |
| created_at | datetime(3) | - | INDEX |
| updated_at | datetime(3) | - | - |

### notifications_mattermost

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | unsigned int | - | PK |
| destination | varchar(50) | - | - |
| text | text | YES | - |
| details | text | YES | - |
| response | text | YES | - |
| hash | char(40) | - | INDEX |
| debug | text | YES | - |
| created_at | datetime(3) | - | INDEX |

---

## System Tables

### system

| Column | Type | Nullable | Default |
|--------|------|----------|---------|
| key | varchar(150) | - | PK |
| value | varchar(400) | - | '' |
| updated_at | datetime(3) | - | CURRENT_TIMESTAMP(3) |

### rsyslog_system_events

| Column | Type | Nullable | Index |
|--------|------|----------|-------|
| id | int unsigned | - | PK |
| facility | tinyint unsigned | YES | - |
| priority | tinyint unsigned | YES | - |
| from_host | varchar(60) | - | - |
| syslog_tag | varchar(60) | - | INDEX |
| message | text | YES | INDEX(500) |
| is_processed | tinyint unsigned | - | INDEX |
| device_reported_at | datetime(3) | YES | - |
| received_at | datetime(3) | YES | - |

---

## Recent Migrations (2025)

| Migration | Description |
|-----------|-------------|
| 2025_07_17 | Create domains table |
| 2025_05_31 | Alter servers (phase 9) |
| 2025_04_29 | Wallet minor DB connection |
| 2025_04_21 | User is_developer field |
| 2025_03_06 | Telegram bot external_id |
| 2025_03_03 | Telegram bots entities table |
| 2025_02_27 | User telegram_user_id |
| 2025_02_06 | Trinity proxy overrides extension |
| 2025_01_21 | Side bet GGR columns |
| 2025_01_11 | KPI sidebet fields |
