---
title: Quick Start (DashPad-API)
---

# Quick Start

This guide provides the fastest way to get the DashPad-API container running on your Linux server using Docker Compose. This setup uses default settings and auto-generated credentials to get you started in minutes.

## Prerequisites

-   **A Linux Server:** The DashPad-API container must run on the Linux-based machine you intend to monitor.

-   **Docker** & Docker **Compose:** You must have a working installation of Docker and Docker Compose on the server.

## Step 1: Create the Docker Compose File

On your Linux server, create a new directory for your DashPad configuration (e.g., `~/dashpad-api/`) and `cd` into it. Inside that directory, create a file named `docker-compose.yml` and paste the following content:

```
version: '3.8'

services:
  dashpad-api:
    # Use the official image from GitHub Container Registry
    image: ghcr.io/mrchrisneal/dashpad-api:latest
    container_name: dashpad-api
    restart: unless-stopped
    # Use host networking for direct access to network interfaces
    # This makes the container's localhost the same as the host's
    network_mode: "host"
    # Volumes are required for persistent data and system access
    volumes:
      # Mount for persistent data (settings, API key, SSL certs)
      - ./data:/data
      # Mount system directories for metric collection (read-only)
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc:/host/etc:ro
      - /var/log:/host/var/log:ro
    # Set your server's timezone
    environment:
      - TZ=America/New_York # IMPORTANT: Change to your server's timezone
```

!!! tip "Timezone Setting" 
    Remember to change the `TZ` environment variable in the `docker-compose.yml` file to match your server's local timezone. You can find a list of valid timezone names [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones "null").

!!! info "Why Host Networking?" 
    Using `network_mode: "host"` simplifies setup by allowing the container to share the host's network stack. This means you can access the API directly via `https://localhost:5241` from the host machine without needing to map ports.

## Step 2: Launch the Container

From the directory containing your `docker-compose.yml` file, run the following command to start the container in the background:

```
docker compose up -d
```

Docker will now pull the latest `DashPad-API` image, create the container, and start the service. On the first run, the container will automatically:

1.  Create a default `settings.json` file inside the `./data` directory.

2.  Generate a secure, 64-character API key.

3.  Generate a self-signed SSL certificate and its fingerprint.


## Step 3: Find Your API Key and SSL Fingerprint

The API key and SSL fingerprint are required to connect from DashPad-Web or any other client. They are printed to the container logs on the first startup.

View the logs to retrieve them:

```
docker logs dashpad-api
```

You will see output similar to this. Copy the **API Key** and the **SSL Fingerprint**.

```
============================================================
SSL CERTIFICATE FINGERPRINT:
A1:B2:C3:D4:E5:F6:A1:B2:C3:D4:E5:F6:A1:B2:C3:D4:E5:F6:A1:B2:C3:D4:E5:F6:A1:B2:C3:D4:E5:F6:A1:B2
============================================================
============================================================
API KEY GENERATED:
1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
Saved to: /data/.env
============================================================
```

## Step 4: Verify the API is Running

You can test that the API is running correctly by making a `curl` request from the server. Use the API key you just retrieved.

```
# Replace YOUR_API_KEY with the key from the logs
curl -k "https://localhost:5241/health?apikey=YOUR_API_KEY"
```

The `-k` flag is necessary to allow `curl` to accept the self-signed certificate. If successful, you should see a JSON response like this:

```
{
  "status": "ok",
  "server_identity": "your-server-hostname",
  "timezone": { ... },
  "server_time": "2025-07-10T21:30:00.123-07:00"
}
```

Congratulations! Your DashPad-API instance is now running and ready to serve data.

## Next Steps

-   [**Configuration**](./configuration.md)**:** Learn how to customize data collection, enable/disable modules, and set alert thresholds by editing the `settings.json` file.

-   [**API Reference**](./api-reference.md)**:** Explore all the available API endpoints in detail.

-   **Connect DashPad-Web:** Use your server's IP address (or `localhost` if on the same machine), the API key, and the SSL fingerprint to configure your [DashPad-Web](../web/configuration.md) instance.
