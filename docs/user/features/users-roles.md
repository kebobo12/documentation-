# User & Role Management

Manage admin panel users, assign roles, and configure permissions.

## Accessing User Management

1. Navigate to **Admin → Users**
2. View list of all admin users

## User List

| Column | Description |
|--------|-------------|
| **ID** | User identifier |
| **Name** | Display name |
| **Email** | Login email |
| **Roles** | Assigned roles |
| **Enabled** | Account status |
| **Last Active** | Last login time |

## Creating a New User

1. Click **Create User**
2. Fill in required fields:

| Field | Required | Description |
|-------|----------|-------------|
| **Name** | Yes | Display name |
| **Email** | Yes | Login email (unique) |
| **Password** | Yes | Initial password |
| **Timezone** | Yes | User's timezone |
| **Enabled** | Yes | Account active |
| **Roles** | No | Assign roles |

3. Click **Save**

## Editing a User

1. Click on user row
2. Update fields as needed
3. Click **Save**

### Resetting Password
1. Edit the user
2. Enter new password in Password field
3. Save

### Disabling a User
1. Edit the user
2. Set **Enabled** to No
3. Save - user can no longer login

## Role Management

### Viewing Roles
1. Navigate to **Admin → Roles**
2. View list of all roles

### Creating a Role
1. Click **Create Role**
2. Enter role name
3. Select permissions to grant
4. Assign users to role
5. Save

### Editing a Role
1. Click on role
2. Modify permissions or users
3. Save

### Default Roles

| Role | Description |
|------|-------------|
| **super_administrator** | Full access (protected) |
| **administrator** | Standard admin access |
| **manager** | Limited management access |
| **viewer** | Read-only access |

> **Note:** The `super_administrator` role cannot be deleted.

## Permission Management

### Viewing Permissions
1. Navigate to **Admin → Permissions**
2. View all available permissions

### Permission Format
Permissions follow the pattern: `{module}_{resource}_{action}`

Examples:
- `wallet_entities_read` - View entities
- `wallet_entities_write` - Edit entities
- `gameserver_games_read` - View games
- `admin_users_write` - Manage users

### Assigning Permissions
Permissions are assigned to roles, not directly to users:
1. Edit a role
2. Check/uncheck permissions
3. Save

## User Settings

### Timezone
Each user has a timezone setting that affects:
- Displayed timestamps
- Date filters
- Report generation

### Session
- Sessions expire after inactivity (default: 2 hours)
- Users are logged out automatically
- Multiple sessions allowed

## Common Tasks

### Grant Admin Access to New Employee
1. Create new user with email
2. Assign appropriate role
3. Share credentials securely
4. User logs in and changes password

### Remove Access for Departed Employee
1. Find user in list
2. Edit and set **Enabled** to No
3. Save - immediate access revocation

### Audit User Activity
1. Go to **Admin → Audit Log**
2. Filter by user ID
3. Review all actions taken

### Check Who's Currently Active
1. Go to **Admin → Users**
2. Sort by **Last Active**
3. Recent timestamps indicate active users
