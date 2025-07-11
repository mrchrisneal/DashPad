---
title: DashPad-API Overview
---

# DashPad-API Overview

DashPad-API is a FastAPI-based backend service that collects system metrics directly from the Linux server it runs on.

!!! warning "DashPad-API Deployment"
    **The DashPad-API container MUST run directly on the server being monitored.** It cannot be deployed to cloud services like Google Cloud Run because it requires:
    
    - Direct access to `/proc` and `/sys` filesystems
    - Local file system access for logs
    - Host networking to gather network information (planned feature)
    
    Only deploy the API container on the actual Linux server you want to monitor.

## Key Features

- **Multi-source data collection** with automatic fallback
- **Modular architecture** for easy metric addition
- **Real-time metrics** with configurable update intervals
- **Built-in caching** for efficient resource usage
- **RESTful API** with automatic documentation
- **Optional authentication** with API keys

## Interactive API Documentation

FastAPI automatically generates interactive API documentation. Access it at:

```
https://your-server:5241/docs
```

This provides a web interface to explore and test all endpoints.

## Architecture

### Module System

Each metric type is implemented as a module:

```
modules/
├── cpu_usage/
├── ram_usage/
├── system_uptime/
├── cron_tasks/
├── logs/
└── alerts/
```

### Data Sources

The API tries to query one or more sources in the order they are specified within `settings.json`, including:

- **Direct** - Read from system files
- **Netdata** - Query Netdata API if available

## Quick Start

### Running Locally

```bash
# With Docker
docker run -d \
  --name dashpad-api \
  -p 5241:5241 \
  -v $(pwd)/settings.json:/app/settings.json:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /var/log:/host/var/log:ro \
  ghcr.io/mrchrisneal/dashpad-api:latest
```

### Basic Usage

By default, a valid API key MUST be passed to the API in either an HTTP header or as a URL parameter (?apikey=your-api-key). Both examples are included below:

```bash
# Check health
curl -k https://localhost:5241/health?apikey=your-api-key

# Get all metrics (with API key)
curl -k -H "X-API-Key: your-api-key" https://localhost:5241/metrics

# View interactive docs
open https://localhost:5241/docs?apikey=your-api-key
```

Note: The `-k` flag is used to accept self-signed certificates.

## Configuration

The API is configured via `settings.json`. Key sections:

- **Metrics**: Which modules to enable
- **Thresholds**: Warning and critical limits
- **Update intervals**: How often to collect data

See [Configuration Reference](configuration.md) for details.

## Security

- Optional API key authentication
- No sensitive data storage
- Read-only system access
- Data redaction capabilities

## Performance

- **Memory**: ~50MB typical usage
- **CPU**: Minimal impact
- **Network**: RESTful JSON responses

## Next Steps

- [Configuration Reference](configuration.md) - Detailed settings
- [API Endpoints](endpoints.md) - Endpoint documentation
- [Available Modules](modules.md) - Module descriptions