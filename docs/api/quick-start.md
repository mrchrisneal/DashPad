---
title: Quick Start (DashPad-API)
---

# Quick Start

This guide provides the fastest way to get the DashPad-API container running on your Linux server using Docker Compose. This setup uses default settings and auto-generated credentials to get you started in minutes.

## Step 0: Satisfy Prerequisites

!!! success "NOTE: Checkboxes on this page can be toggled to keep track of your progress!"

Before continuing, ensure you have the following:

- [ ] **A Linux Server:** The DashPad-API container must run on the Linux-based machine you intend to monitor.

- [ ] **Docker** & Docker **Compose:** You must have a working installation of Docker and Docker Compose on the server.

    - [ ] If you can execute `docker run hello-world` in a terminal without any issues, you've got Docker installed!

    - [ ] If you can execute either `docker compose version` (or `docker-compose --version` on older installations) and get a version number (like `Docker Compose version v2.16.0`), you've got Docker Compose installed!

## Step 1: Create the Docker Compose File

Depending on your Linux distribution, pick one of the following options.

=== "Linux (Generic)"

    On your Linux server, create a new directory for your DashPad configuration (e.g., `~/dashpad-api/`) and `cd` into it:

    ```bash
    mkdir ~/dashpad-api/
    cd ~/dashpad-api/
    ```

    Inside that directory, create a file named `docker-compose.yml` and paste the content in the code block below. This configuration is suitable for most standard Linux distributions like Debian, Ubuntu, or CentOS. 

    The command below opens up the `nano` text editor with "docker-compose.yml" as the filename (you can use another editor if you'd like):

    ```bash
    nano docker-compose.yml
    ```

    Paste the following content into the terminal:

    ```yaml
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

    Hit ++ctrl+x++ then type ++y++, then hit ++enter++ to save the file.

=== "unRAID"
    
    !!! tip "This configuration uses the standard unRAID `appdata` path."

    !!! warning "Docker Compose Plugin Required" 
        For unRAID users, it is highly recommended to use the (free) **Compose Manager** plugin by **dcflachs** to manage your Docker Compose stacks. You can search for "Compose Manager" in the unRAID Community Applications Store, and find more information on the [unRAID forums](https://forums.unraid.net/topic/114415-plugin-docker-compose-manager/). Continue with the instructions below after installing this plugin.

    1. Visit `https://<your-server>/Settings/compose.manager` or `https://<your-server>/Compose` (via the unRAID Web Interface). 

    2. Click the "Add New Stack" button. A modal will appear. Input the desired name of the stack (ex. `DashPad-Web`), then hit the "OK" button.

    3. The stack should now appear on this page. Click the cog (settings) icon to the left of the stack's title, then click "Edit Stack" button. 

    4. When prompted to select a file to edit, click the "Compose File" button. You will be given a blank textarea. 

    5. Copy and paste the contents of the file below:
    
    ```yaml
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
          # Mount for persistent data using the standard unRAID appdata path
          - /mnt/user/appdata/dashpad-api:/data
          # Mount system directories for metric collection (read-only)
          - /proc:/host/proc:ro
          - /sys:/host/sys:ro
          - /etc:/host/etc:ro
          - /var/log:/host/var/log:ro
        # Set your server's timezone
        environment:
          - TZ=America/New_York # IMPORTANT: Change to your server's timezone
    ```


??? tip "Change Your Timezone Setting" 
    Remember to change the `TZ` environment variable in the `docker-compose.yml` file to match your server's local timezone. You can find a list of valid timezone names [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Here are some common timezones to get you started:
    
    - `America/Los_Angeles` (Pacific Time)
    - `America/New_York` (Eastern Time)
    - `Europe/London` (United Kingdom)
    - `Europe/Paris` (France)
    - `Asia/Tokyo` (Japan)
    - `Australia/Sydney` (Australia)
    - `Asia/Kolkata` (India)
    - `America/Sao_Paulo` (Brazil)

!!! info "Why Host Networking?" 
    Using `network_mode: "host"` simplifies setup by allowing the container to share the host's network stack. This means you can access the API directly via `https://localhost:5241` from the host machine without needing to map ports. This parameter also grants DashPad-API additional networking capabilities, mainly used to provide networking info to connected containers (like DashPad-Web).

    Ports 5238 through 5241 are [unassigned](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml?search=5241) as per IANA, so it should be available on your host by default. Regardless, this port can be changed in the `settings.json` file if needed.

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
