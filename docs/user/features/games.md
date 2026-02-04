# Game Management

Manage the game catalog, configure game settings, and control game availability per entity.

## Accessing Games

1. Navigate to **Wallet → Games**
2. Use filters to narrow down the game list

## Game List View

| Column | Description |
|--------|-------------|
| **ID** | Unique game identifier |
| **Name** | Game display name |
| **Provider** | Game provider (e.g., Playson, EGT) |
| **Vendor** | Vendor within provider |
| **Type** | Game type (slots, table, live, etc.) |
| **Status** | Enabled/Disabled |
| **Themes** | Game themes (adventure, fruits, etc.) |
| **Features** | Game features (freespins, bonus, etc.) |

## Filtering Games

- **By Provider** - Select from dropdown
- **By Type** - Slots, table games, live casino, etc.
- **By Status** - Enabled or disabled
- **Search** - Search by name or ID

## Editing a Game

1. Click on a game row to open details
2. Modify fields as needed:

| Field | Description |
|-------|-------------|
| **Name** | Display name |
| **Enabled** | Global enable/disable |
| **Published** | Visible to players |
| **Themes** | Select applicable themes |
| **Features** | Select game features |
| **Jurisdictions** | Enable for specific regions (PE, BR, AR) |
| **Config** | JSON configuration (advanced) |

3. Click **Save**

## Disabling Games for Specific Entities

To disable a game for one entity only (not globally):

1. Go to **Wallet → Entities**
2. Select the entity
3. Go to **Disabled Games** tab
4. Find and toggle the game off

## Game Jurisdictions

Games can be restricted by jurisdiction:

| Jurisdiction | Code | Description |
|--------------|------|-------------|
| Peru | PE | Enabled for Peru market |
| Brazil | BR | Enabled for Brazil market |
| Argentina | AR | Enabled for Argentina market |

## Common Tasks

### Find Games by Provider
1. Go to **Wallet → Games**
2. Use the **Provider** filter dropdown
3. Select the provider name

### Check Game Configuration
1. Open game details
2. Review the **Config** JSON field
3. Check **Details** for additional metadata

### Bulk Operations
Use the checkboxes and action buttons above the table for bulk enable/disable operations.
