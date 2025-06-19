# Available Modules

DashPad provides several monitoring modules that can be enabled and configured independently.

## Available Module Types

- **cpu_usage** - CPU utilization monitoring
- **ram_usage** - Memory usage tracking
- **system_uptime** - System runtime display
- **cron_tasks** - Scheduled task monitoring
- **logs** - System log viewing
- **alerts** - Netdata alert integration

## System Metrics

### CPU Usage

Monitor processor utilization.

**Module Name:** `cpu_usage`

**Data Sources:**
- `direct` - Reads from `/proc/stat`
- `netdata` - Queries Netdata API

**Response Data:**
```json
{
  "usage_percent": 45.2
}
```

**Configuration:**
```json
{
  "metrics": {
    "cpu_usage": {
      "enabled": true,
      "sources": ["direct", "netdata"],
      "thresholds": {
        "warning": 80,
        "critical": 90,
        "direction": "above"
      }
    }
  }
}
```

### RAM Usage

Monitor memory consumption and availability.

**Module Name:** `ram_usage`

**Data Sources:**
- `direct` - Reads from `/proc/meminfo`
- `netdata` - Queries Netdata API

**Response Data:**
```json
{
  "usage_percent": 65.4,
  "used_mb": 10464,
  "total_mb": 16000,
  "available_mb": 5536
}
```

### System Uptime

Track how long the system has been running.

**Module Name:** `system_uptime`

**Data Sources:**
- `direct` - Reads from `/proc/uptime`
- `netdata` - Queries Netdata API

**Response Data:**
```json
{
  "uptime_seconds": 86400,
  "human_readable": "1 day, 0 hrs, 0 mins, 0 secs"
}
```

## Log Monitoring

### System Logs

Monitor and display system log files with real-time updates.

**Module Type:** `logs`

**Features:**
- Configurable line limits
- Incremental updates
- Data redaction for security

**Configuration:**
```json
{
  "logs": {
    "syslog": {
      "path": "/var/log/syslog",
      "lines": 100,
      "redact_datatype": ["ip", "email"]
    }
  }
}
```

## Scheduled Tasks

### Cron Monitoring

Display upcoming cron jobs and scheduled tasks.

**Module Name:** `cron_tasks`

**Features:**
- Reads system cron directories
- Shows next execution time
- Task renaming for clarity

**Response Data:**
```json
{
  "cron_tasks": [
    {
      "name": "Daily Backup",
      "schedule": "0 3 * * *",
      "next_run": "2025-06-19T03:00:00",
      "relative_time_short": "7h43m"
    }
  ]
}
```

## Alert Integration

### Netdata Alerts

Display active system alerts from Netdata monitoring.

**Module Name:** `alerts`

**Response Data:**
```json
{
  "alerts": [
    {
      "name": "Disk Space Warning",
      "status": "WARNING",
      "value": 85.2,
      "units": "%"
    }
  ]
}
```

## Module Status Levels

All modules support status levels for visual indicators:

| Status | Description | Visual Indicator |
|--------|-------------|------------------|
| `normal` | Within thresholds | Green |
| `warning` | Approaching limits | Yellow |
| `critical` | Exceeded thresholds | Red |
| `unknown` | Unable to determine | Gray |

## Creating Custom Modules

Each module follows a standard structure with a coordinator that tries multiple data sources. See the existing modules for examples.

**Note**: Debug logging is currently not functional in the API container.

## Performance Considerations

### Update Intervals

Different modules have different recommended intervals:

| Module Type | Recommended Interval |
|-------------|---------------------|
| CPU Usage | 2-5 seconds |
| RAM Usage | 4-10 seconds |
| System Uptime | 30-60 seconds |
| Logs | 10-30 seconds |
| Cron Tasks | 60 seconds |
| Alerts | 15-30 seconds |

## Troubleshooting Modules

### Module Not Appearing

1. Check if module is enabled in settings.json
2. Verify source configuration
3. Check API logs for errors
4. Test with `/query/{source}/{metric}` endpoint

### Data Collection Failures

Common issues:

| Issue | Solution |
|-------|----------|
| Permission denied | Ensure proper volume mounts |
| File not found | Check path configuration |
| Netdata connection failed | Verify Netdata URL |

## Next Steps

- [API Configuration](configuration.md) - Configure modules
- [Web Interface Guide](../web/index.md) - Display modules