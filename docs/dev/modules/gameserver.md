# Gameserver Module

Game server management including sessions, rounds, transactions, lobbies, multiplayer, and specialized game types (raffle, Emirates Draw).

## Data Model

| Model | Table | Key Fields | Purpose |
|-------|-------|------------|---------|
| `GS_Game` | game | name, type, config | Game definitions |
| `GS_Session` | session | player_id, game_id, started_at | Player sessions |
| `GS_Round` | rounds | session_id, bet, win, status | Gaming rounds |
| `GS_GameTransaction` | game_transactions | round_id, type, amount | Game-level transactions |
| `GS_WalletTransaction` | wallet_transactions | game_transaction_id, amount | Wallet operations |
| `GS_Lobby` | lobbies | name, settings | Game lobbies |
| `GS_LobbyCategory` | lobby_categories | lobby_id, name | Lobby organization |

### Key Relationships

| From | Relation | To | Purpose |
|------|----------|-----|---------|
| GameTransaction | hasMany | WalletTransaction | Financial breakdown |
| GameTransaction | hasOne | TransactionPendingWin | Pending win tracking |
| Lobby | hasMany | LobbyCategory | Categorization |
| LobbyCategory | hasManyThrough | Game | Games in category |
| RaffleSchedule | hasMany | RaffleInstance | Scheduled drawings |

## Routes

**Prefix:** `/gameserver/{environmentSlug}`
**Middleware:** `auth`, `permission:gameserver_*`

| Method | URI | Controller@Method | Purpose |
|--------|-----|-------------------|---------|
| GET | `/` | DashboardController@main | GS dashboard |
| GET | `/games` | GamesController@main | List games |
| GET | `/game-simulator` | GameSimulatorController@main | Simulation tool |
| GET | `/sessions` | SessionsController@main | Session list |
| GET | `/game-transactions` | GameTransactionController@main | Transactions |
| GET | `/lobbies` | LobbiesController@main | Lobby management |
| GET | `/raffle` | RaffleController@main | Raffle system |
| GET | `/emirates-draw` | EmiratesDrawController@main | Emirates Draw |
| GET | `/chat` | ChatController@main | Chat moderation |
| GET | `/multiplayer` | MultiplayerServerController@main | Multiplayer rooms |

## Key Logic

### Game Simulation

**File:** `app/Http/Controllers/Gameserver/GameSimulatorController.php`

Test games without real money:
1. Select game and parameters
2. Run simulation rounds
3. View results and statistics
4. Data stored in `GS_GameSimulation`

### Lobby Management

**File:** `app/Http/Controllers/Gameserver/LobbiesController.php`

Organize games into lobbies:
1. Create lobby with settings
2. Add categories
3. Assign games to categories
4. Configure display order

### Raffle System

**Files:** `app/Models/Gameserver/GS_Raffle*.php`

Scheduled raffle drawings:
- `RaffleSchedule` - Defines drawing schedule
- `RaffleInstance` - Individual drawing execution
- `RaffleTicket` - Player ticket purchases
- Casts: `settings → array`, `state → array`

### Emirates Draw

**Files:** `app/Models/Gameserver/GS_EmiratesDraw*.php`

Lottery-style game integration:
- `EmiratesDrawDrawing` - Drawing results
- `EmiratesDrawTicket` - Tickets with number arrays
- `EmiratesDrawCart` - Shopping cart
- `EmiratesDrawFavourite` - Saved number combinations
- `EmiratesDrawLimit` - Per-entity balance limits

### Multiplayer Rooms

**Files:** `app/Models/Gameserver/GS_Multiplayer*.php`

Real-time multiplayer gaming:
- `MultiplayerGameRoom` - Room configuration
- `MultiplayerGameRound` - Active rounds
- `MultiplayerGameRoomRoutes` - Routing config
- `MultiplayerServerLog` - Server logs

## Services

| Service | Purpose | File |
|---------|---------|------|
| GameServerService | GS API communication | `app/Services/Gameserver/GameServerService.php` |
| EmiratesDrawValidatorService | ED validation | `app/Services/Gameserver/EmiratesDrawValidatorService.php` |

## Configuration

| Env Variable | Default | Purpose |
|--------------|---------|---------|
| gameserver_api_host | - | Per-environment GS API URL |
| gameserver_api_key | - | Per-environment API key |

## Related Modules

- [Wallet](wallet.md) - Entity and transaction source
- [Admin](admin.md) - Permission management
