# Transaction Management

View and search transaction history across the wallet system.

## Accessing Transactions

1. Navigate to **Wallet → Transactions**
2. Select the date range and filters

## Transaction List

| Column | Description |
|--------|-------------|
| **ID** | Transaction identifier |
| **Type** | BET, WIN, REFUND, etc. |
| **Amount** | Transaction amount |
| **Currency** | Transaction currency |
| **Entity** | Associated entity |
| **Player** | Player identifier |
| **Game** | Game played |
| **Round ID** | Associated round |
| **Status** | Completed, Pending, Failed |
| **Created** | Timestamp |

## Filtering Transactions

| Filter | Description |
|--------|-------------|
| **Date Range** | Start and end date |
| **Entity** | Filter by entity |
| **Transaction Type** | BET, WIN, REFUND, etc. |
| **Currency** | Filter by currency |
| **Player ID** | Search specific player |
| **Game ID** | Filter by game |
| **Status** | Filter by status |

## Transaction Types

| Type | Description |
|------|-------------|
| **BET** | Player wager |
| **WIN** | Player winnings |
| **REFUND** | Cancelled/refunded bet |
| **FREESPIN** | Freespin credit |
| **JACKPOT** | Jackpot win |
| **BONUS** | Bonus credit |

## Viewing Transaction Details

1. Click on a transaction row
2. View detailed information:
   - Full transaction data
   - Associated round information
   - Player session details
   - Request/response logs (if available)

## Viewing Rounds

For game round history:

1. Navigate to **Wallet → Rounds**
2. Filter by date, entity, player, or game
3. View round details including all transactions within the round

## Exporting Data

1. Apply your desired filters
2. Click **Export** button above the table
3. Choose format (CSV, Excel)
4. Download the file

## Common Tasks

### Find Transactions for a Player
1. Go to **Wallet → Transactions**
2. Enter the player ID in the filter
3. Set date range
4. Click **Search**

### Investigate a Failed Transaction
1. Find the transaction in the list
2. Click to view details
3. Check the **Error Log** for related errors
4. Review request/response data if available

### Calculate Player Activity
1. Filter by player ID and date range
2. Export the data
3. Sum BET and WIN columns for totals
