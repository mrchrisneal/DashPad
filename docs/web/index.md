---
title: DashPad-Web Overview
---

# DashPad-Web Overview

The DashPad-Web interface is a Svelte 5-based frontend designed primarily for tablet displays, and provides a performant, responsive dashboard for real-time system monitoring. It securely connects to one or more DashPad-API instances to display live metrics, logs, and alerts, turning any spare tablet into a dedicated, always-on monitoring screen. The entire experience is crafted to be lightweight and performant, ensuring it operates effectively even on older hardware.

## Important Concepts

- **Purpose:** DashPad-Web turns any tablet into a dedicated, real-time monitoring screen for your servers. 
- **Security:** DashPad-Web uses a secure proxy for requests to the API container; API keys and credentials are never exposed to the browser. 
- **Multi-Server:** DashPad-Web can monitor multiple `DashPad-API` instances from a single dashboard. 
- **Stateless & (Near) Real-Time:** The web interface has no database, and is limited to the last hour of gathered data. Historical graphs are built in your browser, so the device must remain on with the page in focus for data collection.

!!! tip "In a Hurry? Check out the [**Quick Start Guide**](./quick-start.md) for the fastest way to get up and running."

## Key Features

-   **Tablet-Optimized Layout:** The interface uses a responsive, adjustable grid from 1 to 4 columns to fit any screen size. This ensures optimal use of display real estate on everything from a phone to a large monitor. Module and column widths automatically adapt with orientation, allowing for fluid changes between portrait and landscape modes on portable devices. 
    
-   **Multi-Server Monitoring:** View metrics and logs from multiple DashPad-API instances in a single, unified dashboard. The `startup.sh` script inside the Web container reads environment variables that allow you to configure as many servers as you need (up to 50 by default; only 2 servers have been thoroughly tested). This eliminates   the need to switch between different tabs or tools to monitor your infrastructure.
    
-   **Real-Time Updates:** The interface uses a clever polling mechanism that adjusts its update frequency based on hints from the API (`next_update_sec`). This ensures that frequently changing data like CPU metrics are updated quickly, while less volatile data like upcoming cron jobs are polled less often, resulting in a responsive UI that is also highly efficient. 
    
-   **Historical Sparklines:** Metric modules feature background sparklines that show performance trends over the last 10 minutes (by default; additional toggles for 5 and 15 minute durations are available). This data is collected and stored directly in your browser's memory (`historical.js`) and is capped at 50MB to ensure performance. 
	- **Important:** As all data is stored client-side, the dashboard must remain open and active in your browser to collect data for the sparklines and charts.
    
-   **Drag-and-Drop Customization:** In "Edit Mode," you can easily rearrange modules by dragging and dropping them to create a layout that suits your needs. This functionality is powered by the `svelte-dnd-action` library, and your custom layout is automatically saved to your browser's `localStorage`, making it persistent across sessions.
    
-   **Module-Specific Settings:** Each module has its own set of configurable options, accessible via a cog icon in the module header. For example, you can toggle autoscroll for logs, customize the color thresholds for CPU and RAM warnings, define keyword highlighting rules for log viewers, or set the number of upcoming cron jobs to display. All display settings can be exported to your clipboard and imported later on a different device.


## Technology Stack

-   **Framework:** Built with **Svelte 5** for a highly reactive and performant user experience. As the Svelte framework leverages a compiler, it generates highly optimized, minimal vanilla JavaScript, resulting in a small application bundle and fast load times.
    
-   **Web Server:** Served by **NGINX**, which also acts as a secure reverse proxy to the backend API servers. This is a key part of the security model, as NGINX handles injecting authentication headers, ensuring API keys are never exposed to the frontend.
    
-   **State Management:** Utilizes Svelte stores with `localStorage` persistence for UI state, layout, and module configurations. This keeps the application state consistent and preserves user customizations.
    
-   **Charting:** Native SVG charts are used as a lightweight visualization solution. This avoids heavy, third-party charting libraries, contributing to the small footprint of the application.
    

## Dashboard Layout & Modules

The dashboard is the core of the web interface. Modules are automatically organized into columns, and the number of columns can be changed with just one tap (or click). The layout is fully persistent across sessions, stored directly in your browser.

**Available Module Types:**

-   **System Metrics:** Modules for CPU and RAM usage, featuring charts for real-time values and historical charts when expanded, giving you an at-a-glance view of system health. Chart durations can be toggled between 5 minutes, 15 minutes, 30 minutes (default), and 60 minutes in the Settings pane; they can be set globally and overridden on a per-module basis.
    
-   **Information Display:** A simple module for displaying information like system uptime in a human-readable format.
    
-   **Log Viewer:** A dedicated module for viewing system and application logs in real-time, with support for custom, hexadecimal color codes and keyword highlighting to make finding important events easier.
    
-   **Task Monitoring:** A list-based module that displays upcoming scheduled `cron` tasks and their relative execution times, helping you anticipate system activity.
    
-   **Alerts:** An Alerts module that shows active system warnings and critical alerts from a connected Netdata instance, providing immediate visibility into potential issues. *This module requires an installed and functional Netdata instance.*


## Status Indicators

At-a-glance status information is conveyed through a simple color-coded system, which is shown primarily as a circular indicator icon within each module (where applicable).

🟢 **Green:** Normal operation.
    
🟡 **Yellow:** Warning state, indicating a potential issue (e.g., high CPU usage).
    
🔴 **Red:** Critical state, indicating an active problem that requires attention.

Modules without threshold logic (such as the `uptime` module) do not display threshold indicators. Charts also follow a similar default color scheme; they can be customized in the Settings pane. Threshold states are determined by a connected DashPad-API instance.

## Real-Time Updates

The web interface intelligently polls the DashPad-API container based on `next_update_sec` hints provided by the API itself. This allows the frontend to adapt its polling frequency dynamically, reducing unnecessary network traffic and server load. Default polling intervals are:

-   **Metrics:** ~4 seconds
    
-   **Logs & Alerts:** ~30 seconds
    
-   **Cron Tasks:** ~60 seconds
    
The minimum configurable interval for any data or module type is 2 seconds.

## Performance

DashPad strives to be lightweight, making it ideal for older or resource-constrained devices.

-   **Initial Load:** The application has a small initial footprint, with a transfer size of under 1MB on first page load.
    
-   **Memory Usage:** Active memory consumption of the DashPad-Web container is typically between 10-25MB (on the platform running the container). This is achieved by leveraging an in-memory browser store on the viewing device for historical data rather than a persistent database, which also means the dashboard must remain open to collect trend data.
    

## Security

Security is a core design principle of the web container, handled primarily by the bundled NGINX proxy and the container's `startup.sh` script:

-   **Proxy-Based Architecture:** The web container is (currently) the only component that communicates with API containers. API keys are never sent to the browser. The NGINX proxy injects the required `X-API-Key` header for each request.
    
-   **Authentication:** Access to the web interface is protected by basic authentication (`htpasswd`), configured via environment variables. This provides a simple but effective layer of security for access to the dashboard.
    
-   **Encryption:** Communication is secured with HTTPS. The container generates a self-signed certificate at startup, and it can also validate DashPad-API server certificates via SSL fingerprinting.
    
-   **Masked URLs:** To prevent exposing potentially sensitive information in the UI, server URLs are masked, showing only the first few characters of the hostname. Other information, including keywords and email, IP, & MAC addresses, can be obfuscated within the log module, though this is an API container-based setting.
    

## Deployment

The DashPad-Web container is designed to be deployed using Docker, and runs anywhere containers are supported. Unlike DashPad-API, the Web container does not need direct access to your monitored servers; it only requires network access to your DashPad-API container(s). This separation of concerns makes deployment flexible and secure.

### Cloud Deployment (Google Cloud Run)

In addition to local Docker deployments, DashPad-Web is fully compatible with serverless platforms like Google Cloud Run.

By setting the container to "request-based billing" and permitting it to scale down to zero instances, you are only billed when the dashboard is actively being viewed. Performance testing shows that with request-based billing and minimal resource allocation (0.1 vCPU, 128MB RAM), the container operates with exceptional efficiency: continuous monitoring of two servers at default intervals costs less than **$0.03 per day**, with projected monthly costs under **$1.00** and projected yearly costs under **$10.00**. This makes DashPad an extremely cost-effective solution for a "publicly" accessible, secure monitoring dashboard.

As a reminder, the DashPad-API container must still be deployed on monitored servers so the Web container has a data source.


## Next Steps

-   [**Configuration Guide**](./configuration.md)**:** Learn how to set up and configure the web container.
    
-   [**Usage Guide**](./usage.md)**:** Explore the features of the dashboard and settings panel.
    
-   [**Troubleshooting Guide**](./troubleshooting.md)**:** Find solutions to common issues.
