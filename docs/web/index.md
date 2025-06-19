# DashPad Web Interface Overview

The DashPad Web interface is a Svelte-based frontend designed for tablet displays, providing real-time system monitoring.

## Key Features

- **Tablet-optimized** responsive layout (1-4 columns)
- **Multi-server monitoring** in one dashboard
- **Real-time updates** with automatic polling
- **Background sparklines** for performance trends
- **Drag-and-drop** layout customization
- **Module-specific settings** for each display

## Technology Stack

- **Framework**: Svelte 5
- **Server**: NGINX with proxy
- **State Management**: Svelte stores with localStorage
- **Charts**: Native SVG implementation

## Dashboard Layout

The interface automatically adjusts columns based on screen width:
- **< 768px**: 1 column
- **768-1024px**: 2 columns  
- **1024-1440px**: 3 columns
- **> 1440px**: 4 columns

## Module Types

### System Metrics
**CPU** and **RAM** modules show:
- Current usage percentage
- Visual gauge
- Historical chart (expandable)
- Background sparkline

### Information Display
**System Uptime** shows runtime in compact format

### Log Viewer
**System Logs** with:
- Real-time streaming
- Autoscroll option
- Syntax highlighting
- Incremental updates

### Task Monitoring
**Cron Tasks** display upcoming scheduled tasks

### Alerts
**Netdata Alerts** show active system warnings

## Real-Time Updates

The web interface uses intelligent polling based on API hints:
- Metrics: 4 seconds
- Logs: 30 seconds
- Cron: 60 seconds
- Alerts: 30 seconds

## User Interface

### Settings Panel
- Server management (enable/disable)
- Layout configuration
- Display preferences
- Module settings
- Import/export configuration

### Status Indicators
- **Green**: Normal
- **Yellow**: Warning  
- **Red**: Critical
- **Gray**: Unknown
- **Blue**: Loading

## Browser Support

- Safari on iPad (primary)
- Chrome/Chromium 90+
- Firefox 88+
- Edge 90+

## Security

- No sensitive data stored in browser
- Server URLs masked in UI
- HTTPS enforced for production
- Basic Auth support via NGINX

## Deployment

The Web container can be deployed:
- **Locally** with the API container
- **Cloud** (Google Cloud Run, etc.)
- **Anywhere** Docker runs

Unlike the API container, the Web container doesn't need direct server access.

## Performance

- **Initial Load**: ~500KB
- **Memory**: 30-50MB active

## Configuration

See [Web Configuration](configuration.md) for setup details.

## Next Steps

- [Configuration Guide](configuration.md) - Setup instructions
- [API Documentation](../api/index.md) - Backend reference