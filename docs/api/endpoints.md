# API Endpoints Reference

Complete reference for all DashPad API endpoints.

## Interactive Documentation

FastAPI provides auto-generated interactive API documentation at:

```
https://your-server:5555/docs
```

This allows you to explore and test all endpoints directly in your browser.

## Authentication

If authentication is enabled, include the API key in the request header:

```bash
X-API-Key: your-64-character-hex-key
```

## Base URL

```
https://your-server:5555
```

## Health & Status Endpoints

### GET /health

Basic health check endpoint.

**Response:**
```json
{
  "status": "ok",
  "server_identity": "your-server",
  "timezone": "America/New_York",
  "server_time": "2025-06-18T19:17:00.000-07:00"
}
```

### GET /status

Detailed server status information.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "timezone": "America/New_York",
  "server_info": {
    "hostname": "your-server",
    "platform": "linux",
    "python_version": "3.9.16"
  },
  "api_config": {
    "authentication_enabled": false,
    "cors_origins": ["*"]
  },
  "scheduler_config": {
    "metrics_interval": 4,
    "logs_interval": 30
  },
  "integrations": {
    "netdata": {
      "enabled": true,
      "url": "http://localhost:19999"
    }
  }
}
```

## Metrics Endpoints

### GET /metrics

Returns all enabled metrics in a single response.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "metrics": {
    "cpu": {
      "source": "direct",
      "status": "good",
      "data": {
        "usage_percent": 45.2
      }
    },
    "ram": {
      "source": "direct",
      "status": "warning",
      "data": {
        "usage_percent": 82.5,
        "used_mb": 13200,
        "total_mb": 16000
      }
    },
    "uptime": {
      "source": "direct",
      "status": "good",
      "data": {
        "uptime_seconds": 86400,
        "human_readable": "1 day, 0 hrs, 0 mins, 0 secs"
      }
    }
  },
  "logs": {
    "syslog": {
      "content": {...},
      "total_lines": 5000,
      "returned_lines": 100
    }
  },
  "cron": {
    "cron_tasks": [...],
    "status": "normal"
  },
  "alerts": {
    "alerts": [...],
    "status": "normal"
  },
  "next_update_sec": 4
}
```

### GET /metrics/{metric_name}

Get a specific metric by name.

**Parameters:**
- `metric_name` - Name of the metric (e.g., cpu_usage, ram_usage, system_uptime)

**Response:**
```json
{
  "metric": "cpu_usage",
  "timestamp": "2025-06-18T19:17:00.000-07:00",
  "data": {
    "usage_percent": 45.2
  },
  "source": "direct",
  "status": "good",
  "update_interval_sec": 4
}
```

### GET /query/{source}/{metric}

Query a specific metric from a specific source (for testing).

**Parameters:**
- `source` - Data source: `direct` or `netdata`
- `metric` - Metric name

**Response:**
```json
{
  "metric": "cpu_usage",
  "timestamp": "2025-06-18T19:17:00.000-07:00",
  "data": {
    "usage_percent": 45.2
  },
  "source": "direct",
  "server_identity": "your-server",
  "status": "good"
}
```

## Log Endpoints

### GET /logs

Returns all configured logs with their contents.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "logs": {
    "syslog": {
      "content": {
        "1": "Jun 18 19:15:00 server systemd[1]: Started Daily apt download.",
        "2": "Jun 18 19:16:00 server kernel: [12345.678] CPU0: Temperature above threshold"
      },
      "total_lines": 5000,
      "returned_lines": 100,
      "supports_incremental": true
    }
  },
  "next_update_sec": 30
}
```

### GET /logs/{log_name}

Get a specific log by name.

**Parameters:**
- `log_name` - Name of the log as configured in settings.json
- `since_line` (optional) - Line number to start from (for incremental updates)

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "content": {
    "1001": "Jun 18 19:17:00 server systemd[1]: Log entry...",
    "1002": "Jun 18 19:17:01 server kernel: Another entry..."
  },
  "total_lines": 5000,
  "returned_lines": 2,
  "supports_incremental": true,
  "next_update_sec": 30
}
```

## Cron Endpoints

### GET /cron_tasks

Get upcoming scheduled cron tasks.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "cron_tasks": [
    {
      "name": "Run file mover",
      "schedule": "0 3 * * *",
      "next_run": "2025-06-19T03:00:00",
      "relative_time": "7 hours 43 minutes",
      "relative_time_short": "7h43m"
    }
  ],
  "cron_stats": {
    "total_tasks": 5,
    "next_task_minutes": 463
  },
  "source": "direct",
  "status": "normal",
  "next_update_sec": 60
}
```

## Alert Endpoints

### GET /alerts/netdata

Get active alerts from Netdata.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "alerts": [
    {
      "id": "disk_space_warning",
      "name": "Disk Space Warning",
      "status": "WARNING",
      "value": 85.2,
      "units": "%",
      "info": "Disk usage above 80%",
      "last_status_change": 1718764620
    }
  ],
  "alerts_stats": {
    "total": 2,
    "by_status": {
      "WARNING": 1,
      "CRITICAL": 1
    }
  },
  "source": "netdata",
  "status": "warning",
  "hostname": "your-server",
  "next_update_sec": 30
}
```

## Scheduler Endpoints

### GET /scheduler/status

Get scheduler status and update intervals.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "running": true,
  "update_intervals": {
    "metrics": 4,
    "logs": 30,
    "cron": 60,
    "alerts": 30
  },
  "next_updates": {
    "metrics": 2.5,
    "logs": 15.3,
    "cron": 45.1,
    "alerts": 12.7
  },
  "next_update_sec": 2.5
}
```

### POST /scheduler/control

Control the scheduler (start, stop, restart).

**Parameters:**
- `action` - One of: `start`, `stop`, `restart`

**Response:**
```json
{
  "status": "ok",
  "message": "Scheduler restarted",
  "running": true,
  "server_identity": "your-server"
}
```

## Configuration Endpoints

### GET /settings/modules

Get available modules and their configurations.

**Response:**
```json
{
  "server_identity": "your-server",
  "server_time": "2025-06-18T19:17:00.000-07:00",
  "modules": {
    "metrics": {
      "cpu_usage": {
        "enabled": true,
        "display_name": "CPU Usage",
        "description": "Central Processing Unit utilization",
        "sources": ["direct", "netdata"],
        "thresholds": {
          "warning": 80,
          "critical": 90
        }
      }
    },
    "logs": {
      "syslog": {
        "enabled": true,
        "display_name": "System Log",
        "line_limit": 100,
        "supports_redaction": true
      }
    },
    "cron": {
      "enabled": true,
      "display_name": "Scheduled Tasks"
    },
    "alerts": {
      "enabled": true,
      "display_name": "Active Alerts",
      "sources": ["netdata"]
    }
  },
  "next_update_sec": null
}
```

## Error Responses

### 400 Bad Request

Invalid parameters or request format.

```json
{
  "detail": "Invalid action: invalid_action. Must be one of: start, stop, restart"
}
```

### 401 Unauthorized

Missing or invalid API key (when authentication is enabled).

```json
{
  "detail": "Invalid or missing API key"
}
```

### 404 Not Found

Resource not found.

```json
{
  "detail": "Metric 'non_existent_metric' not found"
}
```

### 500 Internal Server Error

Server error during processing.

```json
{
  "detail": "Internal server error",
  "type": "server_error"
}
```

## Response Headers

All responses include:

- `Content-Type: application/json`
- `Access-Control-Allow-Origin: *` (or configured origins)

## Rate Limiting

No built-in rate limiting. Implement at proxy level if needed.

## Example Usage

### Using cURL

```bash
# Get all metrics
curl -k https://localhost:5555/metrics

# Get specific metric
curl -k https://localhost:5555/metrics/cpu_usage

# With authentication
curl -k -H "X-API-Key: your-api-key" https://localhost:5555/metrics

# Control scheduler
curl -k -X POST "https://localhost:5555/scheduler/control?action=restart"
```

**Note**: The `-k` flag is used to accept self-signed certificates. In production with proper certificates, this flag can be omitted.

### Using Python

```python
import requests

# Base configuration
base_url = "https://localhost:5555"
headers = {"X-API-Key": "your-api-key"}

# Disable SSL verification for self-signed certificates
requests.packages.urllib3.disable_warnings()

# Get all metrics
response = requests.get(f"{base_url}/metrics", headers=headers, verify=False)
data = response.json()

# Get specific log with incremental update
response = requests.get(
    f"{base_url}/logs/syslog",
    params={"since_line": 1000},
    headers=headers,
    verify=False
)
log_data = response.json()
```

### Using JavaScript

```javascript
// Get all metrics
const response = await fetch('https://localhost:5555/metrics', {
  headers: {
    'X-API-Key': 'your-api-key'
  }
});
const data = await response.json();

// Poll for updates
setInterval(async () => {
  const metrics = await fetch('https://localhost:5555/metrics', {
    headers: { 'X-API-Key': 'your-api-key' }
  }).then(r => r.json());
  updateDashboard(metrics);
}, 4000); // Use next_update_sec from response
```

## Next Steps

- [Configuration Reference](configuration.md) - Configure the API
- [Available Modules](modules.md) - Module descriptions