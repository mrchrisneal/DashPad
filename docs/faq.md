---
title: Frequently Asked Questions (FAQ)
---

# Frequently Asked Questions (FAQ)

This page answers commonly asked questions about the DashPad project, including questions pertaining to both the `DashPad-Web` and `DashPad-API` components.

## Licensing & Cost

### Is DashPad free?

- Yes, DashPad is free for **personal, non-commercial, and educational use** under the **GPL v3 license**.

- If you intend to use DashPad in a commercial product, a proprietary application, or for any revenue-generating activities, you **must** purchase a **commercial license**. Please contact [mrchrisneal@gmail.com](mailto:mrchrisneal@gmail.com) for commercial licensing inquiries.

- When in doubt, refer to the `LICENSE.md` file located within all DashPad project repositories.

!!! example "Cloud Deployment Costs"

    If you're planning to host the DashPad-Web container on a platform like Google Cloud, please refer to the Google Cloud Run [Deployment Guide](./web/deployment/cloud-run.md), or see page 10 in the [DashPad Report](./assets/dashpad-report-public.pdf) document for projected and measured costs. 

    Basically, **you** decide how much DashPad-Web will cost to run on cloud platforms!

### What is the difference between personal and commercial use?

- **Personal Use (Free):** This includes running DashPad on your home servers, for personal projects, or for educational purposes. You are free to use, modify, and share the software under the terms of the GPL v3 license.

- **Commercial Use (Paid):** This includes using DashPad in a business environment, integrating it into proprietary software that you sell, or any other context where DashPad is part of a revenue-generating activity.

## System Requirements

### What are the system requirements for running DashPad?

#### Server (Container) Requirements

- A Linux-based server capable of running Docker containers. If you can execute `docker run hello-world` in a terminal without issues, you're probably good to go! 

    - If you have a Linux-based server but don't have Docker installed, you may want to refer to Docker's [official Install page](https://docs.docker.com/engine/install/) for more information.

- Since DashPad was designed to be extremely lightweight, minimal resource allocation is needed. Testing shows usage of less than 50MB of RAM and <1% utilization of *one* CPU core (after startup) for both the API and Web containers. 

- Network connectivity between the `DashPad-Web` and `DashPad-API` containers.

#### Browser Requirements

- While very few devices have been tested, the oldest one so far is an iPad Air 2 from 2014 running iOS 15.8.4; it appears to be fairly responsive and displays the web dashboard without issue (at default update intervals). 

!!! tip "Your performance may vary; try increasing the duration of your update intervals (ex. increase from four to eight or more seconds) if your tablet appears to fall behind or becomes unresponsive."

### How many servers can I monitor with one DashPad-Web instance?

- **Tested Configuration:** The system has been reliably tested with 2+ unRAID servers running concurrently.

- **Theoretical Limit:** The startup script is built to support up to **50** servers.

- **Recommended Limit:** For optimal performance, monitoring between **10-20** servers should be a comfortable upper limit. Performance depends on factors like polling frequency, the number of modules displayed per server, and network latency.

## Privacy & Security

### Does DashPad "phone home" or have any telemetry?

- **No.** DashPad is designed with privacy as a core principle and operates completely on your own infrastructure.

- There is **no external communication** or telemetry of any kind. It can operate entirely on an offline, local network. No data is ever collected or transmitted to external services. All monitoring data stays on your servers.

### How is my data secured?

Since DashPad is a self-hosted solution, you are in complete control of your data and its security. The application provides several security features:

- **HTTPS Support:** Communication is encrypted with HTTPS, supporting both self-signed and CA-issued certificates.

- **API Key Authentication:** Every request to a `DashPad-API` instance is authenticated with a secure, 64-character API key.

- **No Cloud Dependencies:** The core functionality has no reliance on external cloud services.

## Open Source & Development

### Is DashPad open source?

Kind of. DashPad is more aptly described as **"source-available"** under a dual-license model:

- The **GPL v3 license** applies to all personal and non-commercial use.

- A **Commercial License** is required for all other use cases.

The source code is publicly available on GitHub for review, auditing, and personal modification. The project may evolve to a more traditional open-source model as it matures.

### What's the overall status of the project?

As of July 14th, 2025, DashPad is in active, pre-release development. Since the project is created and managed by a solo developer, the primary goal is to establish a stable and coherent codebase that aligns with long-term architectural goals. 

Consequently, pull requests will not be reviewed or accepted at this time. The codebase is likely to undergo significant changes, and this documentation should also be considered a living document. 

Your interest and patience as the project matures is appreciated! 


### Can I contribute to the project?

Community involvement is highly encouraged!

- **Bug Reports & Feature Suggestions:** Please submit these via the GitHub Issues page for the respective repository (`DashPad-Web` or `DashPad-API`).

- **Pull Requests:** Direct pull requests are not being accepted at this time.

- Community feedback is essential and directly influences development priorities. Please see the `CONTRIBUTING.md` file in the repositories for the most current guidelines.

## Usage & Positioning

### Is DashPad a replacement for Grafana, Prometheus, or Zabbix?

**No.** Please do *not* do this! ❤️

DashPad is designed to be a **supplementary** monitoring tool, not a replacement for a comprehensive monitoring stack.

**Use DashPad if you want...** 

✅ Lightweight, customizable, (near) real-time visualizations

✅ System info neatly displayed on smaller displays

✅ A quick, at-a-glance status check of your systems

**Do NOT use DashPad if you need...** 

❌ Long-term data storage

❌ Detailed analytics

❌ Complex querying

❌ Incident management or alerting

For robust, in-depth monitoring, you should use DashPad alongside dedicated solutions like Prometheus, Grafana, or Netdata.

### What are the typical use cases for DashPad?

- **Home Lab Monitoring:** The perfect tool for keeping an eye on your personal servers, NAS, and other home lab equipment.

- **Kiosk Displays:** Its primary purpose is to repurpose old tablets or small screens into dedicated, always-on monitoring dashboards.

- **Supplementary Dashboards:** Use it to create a simple, clean "main overview" dashboard that complements your more detailed Grafana (or similar) dashboards.

## Technical Support

### Where can I get help or report a bug?

- **Documentation:** Your first stop should always be the official documentation. The good news is, you're [already here](https://dashpad.neal.media/)! 👋

- **GitHub Issues:** This is the best place to report bugs or request new features. Please be as detailed as possible in your report, and include container or browser console logs.

- **Email:** For commercial licensing and business inquiries, please contact [mrchrisneal@gmail.com](mailto:mrchrisneal@gmail.com).
