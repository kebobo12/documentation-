# Getting Started

## Logging In

1. Navigate to the Admin panel URL
2. Enter your email and password
3. Click **Login**

> **Note:** After multiple failed login attempts, your account may be temporarily locked.

## First Steps After Login

### Select Environment

The Admin panel manages multiple environments (production, staging, dev, etc.). Select your target environment from the top navigation bar.

### Navigation Overview

The sidebar contains the main menu sections:

| Section | Purpose | Access |
|---------|---------|--------|
| **Wallet** | Entity management, games, transactions | Primary operations |
| **Gameserver** | Game sessions, lobbies, simulations | Game management |
| **Admin** | Users, roles, audit logs, settings | Administration |
| **Backoffice** | Operator user management | Secondary admin |
| **Shared** | Servers, deployment, monitoring | Infrastructure |
| **Sandbox** | Testing environment | Development |

### Change Your Timezone

1. Click on the timezone indicator in the header
2. Select your preferred timezone
3. All timestamps will display in your timezone

## Main Menu Structure

### Wallet Section
- **Entities** - Casino operator clients
- **Entity Groups** - Group operators by company
- **Games** - Game catalog and configuration
- **Game Providers** - Provider settings and overrides
- **Currencies** - Supported currency management
- **Transactions** - Transaction history
- **Rounds** - Gaming round data
- **Error Log** - Error tracking and acknowledgement

### Gameserver Section
- **Dashboard** - Overview and stats
- **Games** - Game server game list
- **Game Simulator** - Test games without real money
- **Sessions** - Player session data
- **Transactions** - Game transactions
- **Lobbies** - Lobby and category management
- **Multiplayer** - Multiplayer room management
- **Raffle** - Raffle system configuration
- **Emirates Draw** - Lottery game management

### Admin Section
- **Users** - Admin user management
- **Roles** - Role configuration
- **Permissions** - Permission management
- **Audit Log** - Action history
- **Event Log** - System events and errors
- **Settings** - System configuration
- **Telegram Bots** - Bot management

### Shared Section
- **Syslog** - System log viewer
- **Databases** - Database status
- **Servers** - Server inventory
- **Servers Control** - Ansible deployment
- **CloudFlare** - DNS management
- **SSL** - Certificate management

## Role-Based Access

| Role | Wallet | Gameserver | Admin | Shared |
|------|--------|------------|-------|--------|
| **Super Administrator** | Full | Full | Full | Full |
| **Administrator** | Full | Full | Limited | View |
| **Manager** | Limited | View | None | None |
| **Viewer** | View Only | View Only | None | None |

> **Note:** The permission system is currently under maintenance. Access may vary.

## Common Tasks

### View Entity Details
1. Go to **Wallet → Entities**
2. Click on an entity name to view details
3. Use tabs to see game providers, disabled games, balance info

### Check Error Logs
1. Go to **Wallet → Error Log** or **Gameserver → Error Log**
2. Use filters to narrow down results
3. Click **Acknowledge** to mark errors as reviewed

### View Transactions
1. Go to **Wallet → Transactions**
2. Use date range and filters
3. Click on a transaction for details

### Manage Users
1. Go to **Admin → Users**
2. Click **Create User** to add new admin
3. Assign roles and set timezone

## Tips

- Use the search function in data tables to find specific records
- Export data using the export buttons above tables
- Bookmark frequently used pages
- Check the Audit Log to see recent changes by other admins

## Getting Help

- Contact your system administrator for access issues
- Check the Event Log for system errors
- Report bugs to the development team
