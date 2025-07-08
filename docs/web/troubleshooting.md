---
title: Troubleshooting Guide (DashPad-Web)
---

# Troubleshooting Guide

This guide provides solutions to common issues you might encounter while setting up or using the DashPad-Web interface.

## The Dashboard is Empty / No Modules are Appearing

This is the most common issue after initial setup. It almost always indicates a problem with the server definitions in your configuration.

**Cause:** The DashPad-Web container failed to connect to or validate any of the `DashPad-API` servers defined in your environment variables.

**Solution:**

1.  **Check the Container Logs:** The first step is always to check the logs of your `dashpad-web` container.

    ```bash
    docker logs dashpad-web
    ```
    
2.  Look for error messages during the startup sequence, such as `API key authentication failed`, `Certificate fingerprint mismatch`, or `Unable to connect to API server`. The logs will tell you exactly which server failed and why.

3.  **Verify Server Configuration:** Open your `docker-compose.yml` or check your `docker run` command and carefully verify the `SERVERX_...` variables for the failing server:

    * Is the `SERVERX_URL` correct and reachable from your DashPad-Web container? Remember to use the `https://` prefix and the correct port (`5241` by default for the API).

    * Is the `SERVERX_KEY` an exact, 64-character hexadecimal string?

    * Are the variables numbered sequentially (e.g., `SERVER1_...`, `SERVER2_...`)?

## Authentication Error (401 Unauthorized)

**Symptom:** The dashboard loads, but a specific server module shows an "Authentication Error" or "401 Unauthorized" message.

**Cause:** The `SERVERX_KEY` provided for that server is incorrect or has expired.

**Solution:**

1.  Retrieve the correct, current API key from the logs of the corresponding `DashPad-API` container.
2.  Update the `SERVERX_KEY` environment variable in your `docker-compose.yml` file for the specific server that is failing.
3.  Restart the `dashpad-web` container: `docker compose up -d --force-recreate`.

## Network Error or Server Unreachable

**Symptom:** A server module shows a "Network Error," "502 Bad Gateway," or "Server Unreachable" message.

**Cause:** The DashPad-Web container cannot establish a network connection to the `DashPad-API` container.

**Solution:**

1.  **Check API Container:** Ensure the `DashPad-API` container is running and healthy on its host machine.
2.  **Verify URL and Port:** Double-check the `SERVERX_URL`. Make sure the IP address and port are correct.
3.  **Check Firewalls:** Ensure that there are no firewall rules on your host or network blocking the connection from the DashPad-Web container to the DashPad-API container on its specified port.

## SSL Fingerprint Mismatch

**Symptom:** The container logs show a `Certificate fingerprint mismatch` error for a specific server, and that server's modules do not appear on the dashboard.

**Cause:** The SSL certificate of the `DashPad-API` server has changed, and its new fingerprint no longer matches the `SERVERX_SSLFINGERPRINT` value you provided. This can happen if the API container was recreated and generated a new self-signed certificate.

**Solution:**

1.  Get the new, correct fingerprint from the `DashPad-API` container's logs.
2.  Update the `SERVERX_SSLFINGERPRINT` value in your `docker-compose.yml` file.
3.  Restart the `dashpad-web` container.

!!! warning "Quick Fix vs. Secure Fix"
    As a temporary fix, you can set `SKIP_SSL_FINGERPRINT_VERIFICATION=true` to bypass this check. However, for security, it is always recommended to use the correct fingerprint.

## Layout or Settings Are Not Saving

**Symptom:** You rearrange modules or change settings, but they revert back to the default state after you refresh the page.

**Cause:** DashPad-Web saves all UI customizations to your browser's `localStorage`. This issue occurs if `localStorage` is disabled or not functioning correctly.

**Solution:**

1.  **Check Browser Mode:** Ensure you are not in a "Private" or "Incognito" browsing mode, as these modes often restrict or clear `localStorage`.
2.  **Enable Cookies/Site Data:** Make sure your browser is configured to allow sites to save data.
3.  **Clear Site Data:** As a last resort, you can try clearing all site data for your DashPad URL through your browser's developer tools or settings, and then reconfigure your layout.
