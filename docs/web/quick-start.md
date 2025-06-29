# Quick Start Guide (DashPad-Web)

This guide will walk you through the fastest way to get the DashPad-Web interface up and running using Docker Compose. The goal is to have a functional dashboard in under 5 minutes.

## Prerequisites

Before you begin, please ensure you have the following:

1. **Docker and Docker Compose:** You must have a working installation of Docker (and ideally Docker Compose) on your system.

2. **A Running DashPad-API Instance:** This guide assumes you have at least one `DashPad-API` container running and accessible on your network. If you haven't set one up yet, please follow the [DashPad-API documentation](../api/index.md) first.


## Step 1: Create the Docker Compose File

Create a new file named `docker-compose.yml` and paste the following content into it. This file defines the DashPad-Web service and all the necessary configurations.

```docker
# TODO: Update the image URL below once it is published on GitHub.
# For now, this assumes you have built the image locally.
version: '3.8'

services:
  dashpad-web:
    image: dashpad-web:latest
    container_name: dashpad-web
    ports:
      - "5432:5432"
    # The 'volumes' section maps a host directory to a directory inside the container.
    # It is highly recommended for performance, as it caches SSL certificates.
    # Format: <PATH_ON_HOST>:<PATH_IN_CONTAINER>
    # For unRAID users: /mnt/user/appdata/dashpad-web:/data
    volumes:
      - ./dashpad-data:/data
    environment:
      # --- Web Interface Authentication ---
      - AUTH_USERNAME=chris
      - AUTH_PASSWORD=password

      # --- API Server Configuration ---
      - SERVER1_NAME=my-server
      - SERVER1_URL=https://<REPLACE_WITH_API_IP>:<REPLACE_WITH_API_PORT>
      - SERVER1_KEY=<REPLACE_WITH_YOUR_64_CHARACTER_API_KEY>

      # To add more servers, copy the three lines above and increment the number.
      # For example, for a second server, you would add:
      # - SERVER2_NAME=another-server
      # - SERVER2_URL=https://<IP_OF_SECOND_SERVER>:<PORT>
      # - SERVER2_KEY=<API_KEY_FOR_SECOND_SERVER>

      # --- Security Settings for Quick Start ---
      # TODO: This variable is used to bypass a buggy SSL fingerprinting feature.
      # The underlying bugs should be addressed in a future version.
      - SKIP_SSL_FINGERPRINT_VERIFICATION=true
    restart: unless-stopped
```

### Step 2: Configure Your Server

You only need to change **two** lines in the `environment` section of the `docker-compose.yml` file you just created:

1.  **`SERVER1_URL`**: Replace `<REPLACE_WITH_API_IP>:<REPLACE_WITH_API_PORT>` with the actual HTTPS URL of your running `DashPad-API` instance.

2.  **`SERVER1_KEY`**: Replace `<REPLACE_WITH_YOUR_64_CHARACTER_API_KEY>` with the API key provided by your `DashPad-API` container.

!!! info "Persistent vs. Ephemeral Storage"
	The volumes section in the docker-compose.yml is highly recommended but optional.
	**With a volume (Recommended)**: SSL certificates from your API servers are cached in the dashpad-data directory. This makes container restarts much faster.
	**Without a volume**: The container will run in "ephemeral mode" and re-download all certificates on every restart. You will see a warning in the logs if this happens.

!!! warning "Security Notice: SSL Verification"
	For this quick start, we are setting `SKIP_SSL_FINGERPRINT_VERIFICATION` to `true`. This simplifies the initial setup but is not recommended for production. Once your dashboard is running, you should disable this option and provide the correct `SERVER1_SSLFINGERPRINT` for each server to ensure you are connecting to the correct, trusted API endpoint.

### Step 3: Launch the Container

Save the changes to your `docker-compose.yml` file. Now, open a terminal in the same directory and run the following command:

```bash
docker compose up -d
```

Docker will now pull (or use the local) image, create the container, and start the DashPad-Web service in the background. If you included the volumes section, it will also create a dashpad-data directory in the same folder to store SSL certificate data.

### Step 4: Access Your Dashboard

Once the container is running, open your web browser and navigate to:

`http://<your_docker_host_ip>:5432`

You will be prompted for a username and password. Use the credentials you specified in the `docker-compose.yml` file (the defaults are `chris` and `password`).

You should now see the DashPad dashboard, and it will begin populating with data from your API server!

### Next Steps

This guide covers the most basic setup. To learn about all the available environment variables, including how to add more servers, configure HTTPS, and enable proper SSL fingerprinting, please see the full [Configuration Guide](./configuration.md).
