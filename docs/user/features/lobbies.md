# Lobby Management

Organize games into lobbies and categories for player-facing game selection.

## Accessing Lobbies

- **Wallet Lobbies:** Navigate to **Wallet → Lobbies**
- **Gameserver Lobbies:** Navigate to **Gameserver → Lobbies**

## Lobby Structure

```
Lobby (e.g., "Main Casino")
├── Category (e.g., "Slots")
│   ├── Game 1
│   ├── Game 2
│   └── Game 3
├── Category (e.g., "Table Games")
│   ├── Game 4
│   └── Game 5
└── Category (e.g., "Live Casino")
    └── Game 6
```

## Lobby List

| Column | Description |
|--------|-------------|
| **ID** | Lobby identifier |
| **Name** | Display name |
| **Slug** | URL-friendly identifier |
| **Categories** | Number of categories |
| **Enabled** | Active status |

## Creating a Lobby

1. Click **Create Lobby**
2. Fill in fields:

| Field | Required | Description |
|-------|----------|-------------|
| **Name** | Yes | Lobby display name |
| **Slug** | Yes | URL identifier |
| **Enabled** | Yes | Active/inactive |
| **Settings** | No | JSON configuration |

3. Click **Save**

## Managing Categories

### Adding a Category
1. Open lobby details
2. Click **Add Category**
3. Enter category name and settings
4. Save

### Editing a Category
1. Click on category name
2. Update name or settings
3. Save

### Reordering Categories
1. Use drag-and-drop to reorder
2. Or update sort order values
3. Save changes

## Adding Games to Categories

1. Open category details
2. Click **Add Games**
3. Search and select games
4. Set display order
5. Save

### Removing Games
1. Open category
2. Find game in list
3. Click remove/delete
4. Confirm

## Lobby Settings

Common settings in the JSON configuration:

| Setting | Description |
|---------|-------------|
| `displayMode` | Grid, list, or carousel |
| `gamesPerPage` | Pagination size |
| `showProviderLogo` | Display provider branding |
| `enableSearch` | Allow game search |
| `enableFilters` | Show filter options |

## Common Tasks

### Create a New Game Category
1. Open the target lobby
2. Add new category
3. Name it (e.g., "New Releases")
4. Add relevant games
5. Set sort order to display first

### Feature a Game
1. Find the game in a category
2. Move to top of sort order
3. Or create a "Featured" category

### Disable a Lobby Temporarily
1. Edit the lobby
2. Set **Enabled** to No
3. Save - lobby hidden from players

### Clone a Lobby Structure
1. Note down existing lobby structure
2. Create new lobby
3. Add same categories
4. Add games to each category
