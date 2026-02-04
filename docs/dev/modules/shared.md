# Shared Module

Infrastructure management including server inventory, deployment automation (Ansible), monitoring (Zabbix), CloudFlare DNS, SSL certificates, and system logging.

## Data Model

| Model | Table | Key Fields | Purpose |
|-------|-------|------------|---------|
| - | servers | hostname, ip_external, region, category | Server inventory |
| - | cf_zones | id, name, status | CloudFlare zones |
| - | cf_zones_dns_records | zone_id, type, name, content | DNS records |
| - | ansible_log | playbook, roles, recap_* | Deployment logs |
| `Admin_RsyslogSystemEvents` | rsyslog_system_events | from_host, syslog_tag, message | System logs |

## Routes

**Prefix:** `/shared`
**Middleware:** `auth`

| Method | URI | Controller@Method | Purpose |
|--------|-----|-------------------|---------|
| GET | `/syslog` | SyslogController@main | View system logs |
| GET | `/databases` | DatabasesController@main | Database status |
| GET | `/monitoring` | MonitoringController@main | Infrastructure status |
| GET | `/deployment` | DeploymentController@main | Deployment control |
| GET | `/servers` | ServersController@main | Server inventory |
| GET | `/servers-control` | ServersControlMachineryController@main | Ansible control |
| GET | `/cloudflare` | CloudFlareController@main | DNS management |
| GET | `/ssl` | SslController@main | SSL certificates |

## Key Logic

### Server Inventory

**File:** `app/Http/Controllers/Shared/ServersController.php`

Track infrastructure:
- Hostname, IPs (external/internal)
- Region (EU, AS, AF, SA, AU)
- Environment (production, beta, staging, dev, local)
- Category (wallet_api, redis, db, haproxy, etc.)
- Zabbix integration for monitoring data

### Zabbix Integration

**File:** `app/Http/Controllers/Shared/ServersController.php`

Monitoring data sync:
```
fetchZabbixHostIds() → Get host IDs from Zabbix
fetchZabbixData() → Retrieve monitoring metrics
syncZabbixHosts() → Sync hosts to local DB
```

**Config:** `ZABBIX_SERVER_URL`, `ZABBIX_API_TOKEN`

### Ansible Deployment

**File:** `app/Http/Controllers/Shared/ServersControlMachineryController.php`

Automated deployment via Ansible:

| Route | Purpose |
|-------|---------|
| `/playbooks` | List available playbooks |
| `/roles` | View Ansible roles |
| `/includes` | Shared includes |
| `/inventories` | Host inventories |
| `/launchpad` | Execute playbooks |
| `/log` | Deployment history |

**Config:**
- `ANSIBLE_PROJECT_ROOT_FOLDER_PATH`
- `ANSIBLE_PLAYBOOKS_FOLDER_RELATIVE_PATH`
- `ANSIBLE_BIN_FOLDER_PATH`
- `ANSIBLE_SSH_PRIVATE_KEY_FILE_PATH`

### CloudFlare DNS Management

**File:** `app/Http/Controllers/Shared/CloudFlareController.php`

DNS zone and record management:
1. Fetch zones from CloudFlare API
2. Sync to local `cf_zones` table
3. Fetch DNS records per zone
4. Store in `cf_zones_dns_records`

**Note:** Hardcoded API credentials in controller (security concern)

### Syslog Viewing

**File:** `app/Http/Controllers/Shared/SyslogController.php`

View rsyslog data:
- Filter by host, tag, message
- Parse facility/priority codes
- Paginated results
- Table: `rsyslog_system_events`

## Services

| Service | Purpose | File |
|---------|---------|------|
| LogService | Centralized logging | `app/Services/Log/LogService.php` |
| DbService | Database operations | `app/Services/Db/DbService.php` |
| SystemService | System utilities | `app/Services/System/SystemService.php` |

## Configuration

| Env Variable | Default | Purpose |
|--------------|---------|---------|
| ZABBIX_SERVER_URL | - | Zabbix API endpoint |
| ZABBIX_API_TOKEN | - | Zabbix auth token |
| ANSIBLE_* | - | Ansible path configs |

## Related Modules

- [Admin](admin.md) - User access control
- [Wallet](wallet.md) - Server allocation for services
