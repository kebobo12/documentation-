# Error Log Management

Monitor, investigate, and acknowledge system errors across wallet and gameserver.

## Accessing Error Logs

- **Wallet Errors:** Navigate to **Wallet → Error Log**
- **Gameserver Errors:** Navigate to **Gameserver → Error Log**

## Error List View

| Column | Description |
|--------|-------------|
| **ID** | Error identifier |
| **Type** | ERROR, WARNING, NOTICE |
| **Message** | Error summary |
| **Exception** | Exception class name |
| **Count** | Occurrence count |
| **First Seen** | First occurrence |
| **Last Seen** | Most recent occurrence |
| **Acknowledged** | Review status |

## Filtering Errors

| Filter | Description |
|--------|-------------|
| **Date Range** | Filter by time period |
| **Type** | ERROR, WARNING, NOTICE, INFO |
| **Acknowledged** | Show acknowledged or pending |
| **Exception Class** | Filter by exception type |
| **Message** | Search in error message |

## Error Details

Click on an error to view:

- **Full Message** - Complete error text
- **Stack Trace** - Code execution path
- **Request Data** - HTTP request that caused error
- **URI** - Endpoint that failed
- **User ID** - Admin user (if authenticated)
- **IP Address** - Request origin

## Acknowledging Errors

After investigating an error:

1. Click the **Acknowledge** button
2. Error moves to "Acknowledged" status
3. Use filters to hide acknowledged errors

### Bulk Acknowledge
1. Select multiple errors using checkboxes
2. Click **Acknowledge Selected**

## Error Types

| Type | Severity | Action |
|------|----------|--------|
| **ERROR** | High | Investigate immediately |
| **WARNING** | Medium | Review when possible |
| **NOTICE** | Low | Monitor for patterns |
| **INFO** | Informational | No action needed |

## Common Error Patterns

### API Timeout Errors
- **Cause:** External API not responding
- **Action:** Check provider status, increase timeout

### Database Connection Errors
- **Cause:** DB server issues
- **Action:** Check database status in Shared → Databases

### Authentication Errors
- **Cause:** Invalid credentials or expired tokens
- **Action:** Verify API keys, check provider config

### Validation Errors
- **Cause:** Invalid request data
- **Action:** Review request payload in error details

## Event Log vs Error Log

| Log | Purpose | Location |
|-----|---------|----------|
| **Error Log** | Application exceptions | Wallet/Gameserver sections |
| **Event Log** | System events and errors | Admin → Event Log |
| **Audit Log** | User actions | Admin → Audit Log |

## Common Tasks

### Find Errors for a Specific Entity
1. Go to Error Log
2. Search for entity ID in message
3. Review related errors

### Monitor New Errors
1. Filter to show only unacknowledged
2. Sort by "Last Seen" descending
3. Review newest errors first

### Investigate Recurring Errors
1. Sort by "Count" descending
2. Focus on high-count errors
3. Check if pattern indicates systemic issue

### Export Error Report
1. Apply filters for desired period
2. Click **Export**
3. Share with development team
