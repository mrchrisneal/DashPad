# Docker Deployment

This guide covers deploying DashPad using Docker on your Linux server.

## Important Notes

⚠️ **The API container MUST run on the server you want to monitor.** It requires direct access to:
- `/proc` filesystem
- `/sys` filesystem  
- System log files
- Host networking

The Web container can run anywhere (local or cloud).

## Prerequisites

- Docker 20.10+
- Docker Compose 1.29+
- Linux server to monitor
- 500MB free disk space

## Quick Deployment

### 1. Create Project Directory

```bash
mkdir -p /opt/dashpad && cd /opt/dashpad
```

### 2. Create docker-compose.yml

```yaml
version: '3.8'

services:
  dashpad-api:
    image: ghcr.io/mrchrisneal/dashpad-api:latest
    container_name: dashpad-api
    restart: unless-stopped
    ports:
      - "5241:5241"
    volumes:
      - ./settings.json:/app/settings.json:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /var/log:/host/var/log:ro
    environment:
      - TZ=America/New_York

  dashpad-web:
    image: ghcr.io/mrchrisneal/dashpad-web:latest
    container_name: dashpad-web
    restart: unless-stopped
    ports:
      - "5240:5240"
    environment:
      - LISTEN_PORT=5240
      - USE_HTTPS=true
      - AUTH_USERNAME=admin
      - AUTH_PASSWORD=password
      - SERVER1_NAME=Local Server
      - SERVER1_URL=https://dashpad-api:5241
      - SERVER1_KEY=your-64-character-api-key-from-api-logs
      - SERVER1_SSLFINGERPRINT=your-ssl-fingerprint-from-api-logs
    depends_on:
      - dashpad-api
```

### 3. Create settings.json

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
  }
}
```

### 4. Start Services

```bash
docker-compose up -d
```

## Volume Mounts

The API container needs read-only access to system directories:

```yaml
volumes:
  # Configuration
  - ./settings.json:/app/settings.json:ro
  
  # System metrics (required)
  - /proc:/host/proc:ro
  - /sys:/host/sys:ro
  
  # Logs (optional)
  - /var/log:/host/var/log:ro
  
  # unRAID specific (optional)
  - /var/local/emhttp:/host/var/local/emhttp:ro
  - /etc/cron.d:/host/etc/cron.d:ro
```

## Multi-Server Monitoring

To monitor multiple servers, run an API container on each server:

**Server 1:**
```bash
docker run -d \
  --name dashpad-api-server1 \
  -p 5241:5241 \
  -v ./settings.json:/app/settings.json:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  dashpad-api
```

**Server 2:**
```bash
docker run -d \
  --name dashpad-api-server2 \
  -p 5556:5241 \
  -v ./settings.json:/app/settings.json:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  dashpad-api
```

**Central Web Container:**
```bash
docker run -d \
  --name dashpad-web \
  -p 5240:5240 \
  -e LISTEN_PORT=5240 \
  -e USE_HTTPS=true \
  -e AUTH_USERNAME=admin \
  -e AUTH_PASSWORD=password \
  -e SERVER1_NAME="Server 1" \
  -e SERVER1_URL="https://server1:5241" \
  -e SERVER1_KEY="api-key-from-server1-logs" \
  -e SERVER1_SSLFINGERPRINT="ssl-fingerprint-from-server1-logs" \
  -e SERVER2_NAME="Server 2" \
  -e SERVER2_URL="https://server2:5241" \
  -e SERVER2_KEY="api-key-from-server2-logs" \
  -e SERVER2_SSLFINGERPRINT="ssl-fingerprint-from-server2-logs" \
  dashpad-web
```

## Platform-Specific Notes

### unRAID

Add via Docker template or use command line with appropriate paths.

### Synology NAS

1. Install Docker from Package Center
2. Create shared folder `/docker/dashpad`
3. Use Container Manager to deploy

### Standard Linux

Works on any Linux distribution with Docker installed.

## Security

### Enable API Authentication

API authentication is enabled by default. To get your API key and SSL fingerprint:

1. Check API container logs:
   ```bash
   docker logs dashpad-api | grep -E "API Key:|SSL Fingerprint:"
   ```

2. Use these values in your Web container configuration:
   ```yaml
   environment:
     - SERVER1_KEY=your-64-character-key-from-logs
     - SERVER1_SSLFINGERPRINT=your-ssl-fingerprint-from-logs
   ```

## Maintenance

### View Logs

```bash
docker-compose logs -f dashpad-api
docker-compose logs -f dashpad-web
```

### Update Containers

```bash
docker-compose pull
docker-compose up -d
```

### Check Status

```bash
docker-compose ps
```

## Troubleshooting

### Container Won't Start

Check logs:
```bash
docker logs dashpad-api
```

Common issues:
- Port already in use
- Permission denied on volume mounts
- Invalid settings.json

### No Data Showing

Verify:
- API is accessible: `curl -k https://localhost:5241/health`
- Correct volume mounts
- Settings.json is valid JSON
- API key and SSL fingerprint are correct in Web container

## Next Steps

- [API Configuration](api/configuration.md) - Configure metrics
- [Web Configuration](web/configuration.md) - Configure interface