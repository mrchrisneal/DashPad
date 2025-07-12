---
title: DashPad-API Overview
---

# DashPad-API Overview

The DashPad-API is a lightweight, secure, and efficient backend service built with Python and FastAPI. It serves as the data collection engine for the DashPad ecosystem, running directly on your Linux servers to gather system metrics, logs, and other vital information. It then exposes this data through a clean, RESTful API, ready to be consumed by the [DashPad-Web](../web/index.md) interface.

!!! abstract "Important Concepts" 

    - **Purpose:** DashPad-API is the backend data collector. It runs on a server, gathers metrics, and provides them to the web interface. 
    - **Deployment:** The API container **must** run directly on the Linux server you want to monitor. It cannot be deployed to a separate machine or a cloud service. 
    - **Configuration:** All configuration is managed through a single `settings.json` file, giving you granular control over every aspect of data collection. 
    - **Data Sources:** It intelligently pulls data from multiple sources, including direct system files (`/proc`, `/sys`) and the Netdata API, with automatic fallback (assuming a valid Netdata instance is installed/running).
    - **Security:** Communication is secured with HTTPS and a mandatory 64-character API key, both of which are automatically generated on first run.

!!! tip "In a Hurry? Check out the [**Quick Start Guide**](./quick-start.md) for the fastest way to get up and running."

!!! danger "Deployment Requirement" 
    The DashPad-API container **must** run directly on the server being monitored. This is not optional. It is designed to have low-level access to the system's filesystems to collect accurate data:

    - **Direct Filesystem Access:** It reads directly from `/proc` and `/sys` for core metrics like CPU, RAM, and system uptime.
    - **Log File Access:** It requires access to system log files located in directories like `/var/log`.
    - **Future-Proofing:** This design allows for future modules that may require deeper system integration.


## Key Features

-   **Multi-Source Data Collection:** Automatically selects a data source in a customizable order (`direct` file access or `Netdata` API) for each metric, ensuring reliability and flexibility.

-   **Efficient Caching:** In-memory caching minimizes system load by reducing redundant data collection for frequent requests.

-   **Modular Architecture:** Each metric, log, or data type is a self-contained module, making the system easy to maintain and extend.

-   **Secure by Default:** Enforces HTTPS and requires API key authentication for all endpoints. SSL certificates and a strong API key are generated automatically on first launch.

-   **Interactive API Docs:** Leverages FastAPI to provide auto-generated, interactive API documentation, making it easy to explore and test endpoints directly from your browser.

-   **Dynamic Configuration:** All behavior is controlled via the `settings.json` file, allowing for detailed customization without rebuilding the container.


## Architecture Summary

The API is built around a few core components that work together to provide data efficiently and reliably.

-   **Scheduler:** A background process that periodically collects data for all enabled modules based on the intervals you define in `settings.json`. This ensures that data is always fresh without constantly polling system files.

-   **Cache:** Once collected, the processed data for each module is stored in a simple in-memory cache. When the web interface requests data, it is served directly from this fast cache, preventing high system load.

-   **Module System:** Each type of data (CPU, RAM, Logs, etc.) is handled by a dedicated module. Each module knows how to collect its specific data from different sources (currently either directly or from the Netdata API) and how to process it into a standard format.

-   **API Endpoints:** The FastAPI application exposes the cached data through a set of RESTful endpoints. It also handles incoming requests, authentication, and response formatting.


## Accessing the Interactive API Docs

FastAPI automatically generates a user-friendly, interactive interface for the API. Once the container is running, you can access it in your browser to see all available endpoints, view their schemas, and test them live.

Access the interactive documentation at:

`https://<your_server_ip>:5241/docs`

!!! warning "Authentication Notice"
    At the top of the interactive docs page, you will see text stating that authentication is required. While the documentation page is public, interacting with the API (using interactive demos) requires authentication.

While the documentation page itself is visible to anyone, **you must authenticate** (with your API key) to use the "Try it out" feature for any of the endpoints.

Listed below are three primary methods for authenticating with the interactive documentation.

!!! question "How Do I Authenticate With Interactive Documentation?"

    !!! tip "You should have your generated [**DashPad-API key**](./quick-start/#step-3-find-your-api-key-and-ssl-fingerprint) handy (perhaps even saved somewhere) before proceeding! This key is 64 characters long, and can be found in the DashPad-API container logs, printed as part of the startup process."

    === "Method 1: Authorize Button"
    
        **Use the "Authorize" Button (Recommended)**
        
        This is the easiest and most convenient way to authenticate your session directly within the docs page.
        
        1. Click the **Authorize** button (with a lock icon) located at the top right of the page.
        2. An "Available authorizations" dialog will appear. You can use any of the defined schemes, but `BearerAuth` is often the simplest. In the `BearerAuth` section, paste your 64-character API key into the **Value** field.
        3. Click the **Authorize** button in the dialog, and then **Close**.
        
        The lock icon on the Authorize button will now appear "closed," indicating that you are authenticated. All "Try it out" requests you make from the page will now automatically include the correct `Authorization: Bearer <your-api-key>` header.
    
    === "Method 2: URL Parameter"
    
        **Use a URL Parameter (Quick Access)**
        
        For quick, one-off access, you can provide the API key as a URL parameter. This is useful for sharing a direct link to the docs.
        
        Simply append `?apikey=` followed by your key to the URL:  
        `https://<your_server_ip>:5241/docs?apikey=<YOUR_API_KEY>`
        
        !!! warning "While convenient, be mindful of sharing URLs that contain your API key."
    
    === "Method 3: Browser Extension"
    
        **Use a Browser Extension (Advanced)**
        
        For developers who frequently interact with the API, using a browser extension to set the request header is a powerful option. This method allows you to set the API key once for the domain, and it will be automatically included in every request.
        
        1. Install a browser extension that can modify request headers (e.g., "ModHeader" for Chrome/Firefox).
        2. Configure the extension to add one of the following headers for all requests to your API's domain (`<your_server_ip>:5241`):
            - **Header Name:**  `X-API-Key`
            - **Header Value:**  `<YOUR_API_KEY>`
        3. With the extension active, the interactive docs will be automatically authenticated on every visit.

## Next Steps

-   [**Quick Start**](./quick-start.md)**:** Get the API container up and running in minutes.

-   [**Configuration**](./configuration.md)**:** A deep dive into the `settings.json` file.

-   [**API Reference**](./api-reference.md)**:** Detailed documentation for every API endpoint.