# Game Provider Management

Configure game providers, manage API settings, and set up entity-specific overrides.

## Accessing Game Providers

1. Navigate to **Wallet → Game Providers**
2. View the list of all configured providers

## Provider List

| Column | Description |
|--------|-------------|
| **ID** | Provider identifier |
| **Name** | Provider name |
| **Enabled** | Global status |
| **Maintenance** | Maintenance mode flag |
| **Timeout** | API timeout (seconds) |
| **Vendors** | Associated vendors |

## Provider Settings

### General Settings

| Field | Description |
|-------|-------------|
| **Name** | Provider display name |
| **Enabled** | Enable/disable globally |
| **Maintenance Mode** | Put provider in maintenance |
| **Timeout Seconds** | API timeout setting |
| **Trust Custom Headers** | Accept custom HTTP headers |

### API Configuration

| Field | Description |
|-------|-------------|
| **API URL** | Provider API endpoint |
| **API Key** | Authentication key |
| **API Hostname Header** | Custom hostname header |

## Entity-Specific Overrides

To configure a provider differently for specific entities:

1. Go to **Wallet → Game Provider Configs**
2. Click **Create** or edit existing
3. Select:
   - **Entity** - Target entity
   - **Game Provider** - Provider to configure
4. Set override values (API credentials, settings)
5. Save

## Maintenance Mode

When a provider has issues:

1. Go to **Wallet → Game Providers**
2. Find the provider
3. Enable **Maintenance Mode**
4. Players will see maintenance message instead of games

To restore:
1. Disable **Maintenance Mode**
2. Games become available again

## Trinity Proxy (Evolution)

For Evolution gaming integration:

1. Go to **Wallet → Trinity Entity Overrides**
2. Configure entity-specific Evolution settings
3. Manage proxy servers, Redis, and database replicas

### Proxy Components

| Component | Purpose |
|-----------|---------|
| **Proxy Servers** | Main Evolution proxy |
| **Redis Servers** | Session storage |
| **DB Replicas** | Database replication |
| **API Servers** | API routing |

## Vendor Groups

Organize vendors within providers:

1. Go to **Wallet → Game Provider Vendor Groups**
2. Create groups to organize vendors
3. Set calculation rules for revenue

## Common Tasks

### Disable a Provider Temporarily
1. Find provider in list
2. Toggle **Enabled** to off
3. Or enable **Maintenance Mode** for softer disable

### Check Provider API Status
1. Go to **Wallet → System**
2. Use **API Call** to test provider endpoint
3. Review response

### Update Provider Credentials
1. Edit the provider
2. Update API URL or API Key
3. Save
4. Test with an API call
