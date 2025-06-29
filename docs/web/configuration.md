---
title: Configuration (DashPad-Web)
---

# Configuration

This guide covers configuring the DashPad Web interface.

## Container Configuration

The Web container uses environment variables for configuration.

### Basic Configuration

| Variable | Description | Example |
|----------|-------------|---------|
| `LISTEN_PORT` | NGINX listen port | `5432` |
| `USE_HTTPS` | Enable HTTPS | `true` |
| `AUTH_USERNAME` | Basic auth username | `admin` |
| `AUTH_PASSWORD` | Basic auth password | `secure-password` |
| `SKIP_SSL_FINGERPRINT_VERIFICATION` | Skip SSL verification | `false` |

### Server Configuration

Servers are configured using numbered environment variables:

**Server 1:**
- `SERVER1_NAME` - Display name
- `SERVER1_URL` - API URL (with https://)
- `SERVER1_KEY` - 64-character API key
- `SERVER1_SSLFINGERPRINT` - SSL certificate fingerprint

**Server 2:**
- `SERVER2_NAME` - Display name
- `SERVER2_URL` - API URL
- `SERVER2_KEY` - API key
- `SERVER2_SSLFINGERPRINT` - SSL fingerprint

Continue pattern for additional servers (SERVER3_*, SERVER4_*, etc.)

### Docker Compose Example

```yaml
version: '3.8'

services:
  dashpad-web:
    image: ghcr.io/mrchrisneal/dashpad-web:latest
    ports:
      - "5432:5432"
    environment:
      - LISTEN_PORT=5432
      - USE_HTTPS=true
      - AUTH_USERNAME=admin
      - AUTH_PASSWORD=password
      
      # Server 1
      - SERVER1_NAME=Production
      - SERVER1_URL=https://192.168.1.10:5555
      - SERVER1_KEY=a4514a337c35c834dc4703f286efd90118aaa568bf324ffd7aedaeba61d7a679
      - SERVER1_SSLFINGERPRINT=F0:B5:B7:98:59:35:42:4B:59:28:47:62:83:EB:BA:DD
      
      # Server 2
      - SERVER2_NAME=Development
      - SERVER2_URL=https://192.168.1.20:5555
      - SERVER2_KEY=b1b8908f1754f512e24f2355e405c73fc4ee85fdefae8674363e69bddc6d39b3
      - SERVER2_SSLFINGERPRINT=4F:B4:5C:24:91:56:D5:B3:93:DE:53:DE:1B:95:4B:2E
```

### Getting API Keys and SSL Fingerprints

1. Deploy the API container first
2. Check API container logs for the generated API key and SSL fingerprint
3. Use these values in the Web container configuration

## User Interface Settings

### Dashboard Layout

Configure through the Settings panel:
- **Columns**: 1-4 based on screen size
- **Module Layout**: Drag to reposition
- **Expand/Collapse**: Individual module control

### Chart Settings

**Duration Options**:
- Expanded charts: 5, 15, 30, or 60 minutes
- Background sparklines: 5, 10, or 15 minutes

**Data Retention**:
- 15 minutes: Low-memory devices
- 30 minutes: Tablets
- 60 minutes: Default
- 120 minutes: High-memory devices

## Module-Specific Settings

### Log Modules

```javascript
{
  "autoscroll": true,
  "highlightRules": [
    {
      "keyword": "error",
      "color": "#ff4444",
      "enabled": true
    }
  ]
}
```

### Metric Modules

- Autoscale: Dynamic Y-axis
- Duration: Override global setting

### Alert Modules

- Show past alerts
- Maximum alert history

## Security

### HTTPS

The container auto-generates self-signed certificates when `USE_HTTPS=true`.

### Basic Authentication

Always configure authentication:

```yaml
environment:
  - AUTH_USERNAME=yourusername
  - AUTH_PASSWORD=strongpassword
```

### SSL Verification

- Set `SKIP_SSL_FINGERPRINT_VERIFICATION=false` for production
- Use `true` only for testing or if certificates change frequently

## Import/Export

### Export Settings
1. Open Settings → Import/Export
2. Click "Export Configuration"
3. Save JSON file

### Import Settings
1. Open Settings → Import/Export
2. Select saved JSON
3. Click "Import Configuration"

## Troubleshooting

### Servers Not Appearing

Check environment variables are set correctly:
- Each server needs all 4 variables (NAME, URL, KEY, SSLFINGERPRINT)
- URLs must include protocol (https://)
- API keys must be exactly 64 characters

### Connection Issues

- Verify API containers are running
- Check firewall rules
- Confirm SSL fingerprints match
- Test with `SKIP_SSL_FINGERPRINT_VERIFICATION=true`

## Best Practices

- Always use HTTPS in production
- Configure strong passwords
- Keep SSL fingerprint verification enabled
- Regular configuration backups

## Next Steps

- [API Configuration](../api/configuration.md) - Backend setup
- [Docker Deployment](../docker.md) - Local deployment