# DashPad

A modular system monitoring solution designed to repurpose old tablets as dedicated system metrics displays.

## 🚧 IMPORTANT NOTE

**DashPad is currently in pre-release development.** This project is actively maintained by a single developer and is not yet ready for public use. Please expect:

- Frequent changes to code and documentation
- Potential bugs and incomplete features
- Documentation that may be outdated or incomplete

Public release is planned following bug fixes and documentation completion.

## What is DashPad?

DashPad transforms unused tablets into real-time system monitoring displays for your servers. Built with a microservices architecture, it provides comprehensive infrastructure monitoring with minimal resource usage.

### Key Features

- **Real-time Monitoring**: Updates every 2-60 seconds
- **Tablet Optimized**: Responsive interface designed for iPad and other tablets
- **Multi-Server Support**: Monitor multiple systems from one dashboard
- **Modular Design**: Enable only the metrics you need
- **Lightweight**: Minimal resource usage on both server and tablet

## DashPad is for you if...

- You have old tablets gathering dust
- You want lightweight system monitoring without a database
- You prefer simple, visual monitoring displays
- You run home servers or small infrastructure
- You value resource efficiency
- You want at-a-glance system health visibility

## DashPad is NOT for you if...

- You need historical data storage or trending
- You require complex alerting rules
- You want a full monitoring stack (use Prometheus/Grafana instead)
- You need Windows support
- You require user management or multi-tenancy
- You need to monitor many dozens of servers 

## Architecture Overview

DashPad consists of two primary microservices:

| Component | Description | Deployment |
|-----------|-------------|------------|
| **[DashPad-API](api/index.md)** | Backend data collection service | Local server only |
| **[DashPad-Web](web/index.md)** | Frontend monitoring interface | Local or cloud |

**Important**: The API container must run directly on the Linux server being monitored, as it requires direct access to system files like `/proc`. Only the Web container can be deployed to the cloud.

## Available Metrics

- **System Metrics**: CPU usage, RAM usage, system uptime
- **Log Monitoring**: Real-time system log viewing
- **Scheduled Tasks**: Cron job monitoring
- **Alerts**: Netdata integration for system alerts

## Documentation

- **[Architecture](architecture.md)** - Technical design overview
- **[API Configuration](api/configuration.md)** - Backend setup
- **[Web Configuration](web/configuration.md)** - Frontend setup
- **[API Reference](api/endpoints.md)** - Endpoint documentation

## Support

- **Documentation**: You're here!
- **Source Code**: [GitHub Repository](https://github.com/mrchrisneal/DashPad)
