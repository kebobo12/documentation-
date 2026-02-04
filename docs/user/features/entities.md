# Entity Management

Entities represent casino operator clients in the system. Each entity has its own configuration, balance limits, and game provider settings.

## How It Works

Entities are the core business objects in the Wallet system. They represent individual casino operators who use the platform. Each entity can belong to one or more entity groups and can have customized game provider configurations.

## Getting Started

1. Navigate to **Wallet → Entities**
2. The list shows all entities for the selected environment

## Entity List View

| Column | Description | Sortable | Filterable |
|--------|-------------|----------|------------|
| **ID** | Unique identifier | Yes | Yes |
| **Name** | Entity display name | Yes | Yes (search) |
| **Status** | Active/Inactive | Yes | Yes (dropdown) |
| **Balance Limit** | GGR usage limit | Yes | No |
| **Used Balance** | Current usage | Yes | No |
| **Created** | Creation date | Yes | Yes (date range) |

## Create / Edit Entity

Required fields are marked with *

| Field | Type | Description |
|-------|------|-------------|
| **Name*** | Text input | Entity display name |
| **Status*** | Dropdown | Active or Inactive |
| **GGR Usage Limit** | Number | Maximum GGR allowed |
| **Config** | JSON editor | Advanced entity configuration |

## Entity Details View

After selecting an entity, you can view:

### General Info Tab
- Basic entity information
- Current balance and limits
- Status and configuration

### Game Providers Tab
- Configured game providers for this entity
- Override settings per provider
- Enable/disable providers

### Disabled Games Tab
- Games disabled for this entity
- Toggle games on/off
- Bulk enable/disable

### Balance History Tab
- Balance limit changes over time
- Who made changes
- Timestamps

## Common Tasks

### Disable a Game for an Entity
1. Open entity details
2. Go to **Disabled Games** tab
3. Find the game in the list
4. Click the toggle to disable/enable

### Change Balance Limit
1. Open entity details
2. Update the **GGR Usage Limit** field
3. Click **Save**

### View Entity Group Membership
1. Open entity details
2. Check the **Entity Groups** section
3. See which groups this entity belongs to

## Permissions

| Role | Can View | Can Create | Can Edit | Can Delete |
|------|----------|------------|----------|------------|
| Admin | Yes | Yes | Yes | Yes |
| Manager | Yes | Yes | Yes | No |
| Viewer | Yes | No | No | No |

## Limitations

| Constraint | Limit |
|------------|-------|
| Name length | 255 characters |
| Config JSON size | 65KB |
| Disabled games per entity | Unlimited |

## FAQ

**Q: Can I move an entity between groups?**
A: Yes, go to Entity Groups and update the membership.

**Q: What happens when balance limit is exceeded?**
A: The entity will receive notifications and may have restricted functionality depending on configuration.

**Q: Can I bulk edit multiple entities?**
A: Currently, entities must be edited individually.
