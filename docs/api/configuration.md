---
title: Configuration (DashPad-API)
---

# Configuration

This document covers configuring the DashPad API through the `settings.json` file.

## Configuration File

The `settings.json` file should be placed in the API container root:
- **Docker**: Mount as volume at `/app/settings.json`
- **Local Development**: Place in project root

## Core Configuration Sections

### API Server Settings

```json
{
  "api": {
    "host": "0.0.0.0",
    "port": 5241,
    "allow_origins": ["*"],
    "auth": {
      "enabled": true,
      "api_key": "your-64-character-hex-key"
    }
  }
}
```

**Note**: API authentication is enabled by default. The API key is auto-generated on first startup and displayed in the container logs.

### Metrics Configuration

Control which metrics are collected:

```json
{
  "metrics": {
    "update_interval_seconds": 4,
    "enabled_metrics": ["cpu_usage", "ram_usage", "system_uptime"],
    "sources": {
      "cpu_usage": ["direct", "netdata"],
      "ram_usage": ["direct", "netdata"],
      "system_uptime": ["direct"]
    }
  }
}
```

### Threshold Configuration

Define warning and critical levels:

```json
{
  "metrics_thresholds": {
    "cpu_usage": {
      "warning": 80,
      "critical": 90,
      "direction": "above"
    },
    "ram_usage": {
      "warning": 80,
      "critical": 90,
      "direction": "above"
    }
  }
}
```

Direction can be:
- `"above"`: Alert when value exceeds threshold
- `"below"`: Alert when value falls below threshold

### Log Configuration

Configure log file monitoring:

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

### Cron Configuration

Monitor scheduled tasks:

```json
{
  "cron": {
    "locations_types_map": {
      "/etc/cron.d/": "auto"
    },
    "rename_map": {
      "backup": "Daily Backup Task"
    }
  }
}
```

### Netdata Integration

If using Netdata as a data source:

```json
{
  "netdata": {
    "url": "http://localhost:19999"
  }
}
```

## Complete Example

Here's a minimal working configuration:

```json
{
  "api": {
    "host": "0.0.0.0",
    "port": 5241
  },
  "metrics": {
    "update_interval_seconds": 4,
    "enabled_metrics": [
      "cpu_usage",
      "ram_usage",
      "system_uptime"
    ]
  },
  "metrics_thresholds": {
    "cpu_usage": {
      "warning": 80,
      "critical": 90,
      "direction": "above"
    },
    "ram_usage": {
      "warning": 80,
      "critical": 90,
      "direction": "above"
    }
  },
  "logs": {
    "syslog": {
      "path": "/var/log/syslog",
      "lines": 100
    }
  }
}
```

## Environment Variables

Some settings can be overridden:

| Variable | Overrides | Example |
|----------|-----------|---------|
| `API_HOST` | `api.host` | `0.0.0.0` |
| `API_PORT` | `api.port` | `5241` |
| `TZ` | System timezone | `America/New_York` |

## Path Types

When configuring file paths, use the appropriate type:

- `"direct"`: Use path exactly as specified
- `"bindmount"`: Prepend `/host/` to the path
- `"auto"`: Automatically detect container environment

## Data Redaction

Available redaction types for logs:
- `ip`: Redact IP addresses
- `mac`: Redact MAC addresses  
- `email`: Redact email addresses
- `url`: Redact URLs

## Validation

The API validates configuration on startup. Common errors:
- Invalid JSON syntax
- Unknown configuration keys
- Invalid threshold values

Check logs if the API fails to start:
```bash
docker logs dashpad-api
```

## Next Steps

- [API Endpoints](endpoints.md) - Endpoint reference
- [Available Modules](modules.md) - Module configurations