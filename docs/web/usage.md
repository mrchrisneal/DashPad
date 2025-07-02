---
title: Usage Guide (DashPad-Web)
---

# Usage Guide

This guide covers day-to-day use of the DashPad-Web interface. Once you have successfully deployed and configured your container (perhaps using the [Quick Start](./quick-start.md) or [Configuration](./configuration.md) guides), this document will help you utilize DashPad to its full potential.

## Navigating the Dashboard

The main dashboard is composed of a header for global controls, a bar beneath it that displays currently monitored servers (if multiple are configured), and of course, a grid of monitoring modules. The dashboard is designed to be left open on a dedicated display, like a tablet, providing you with an always-on, at-a-glance view of your systems' health.

### Header Bar Controls

At the top of the page are the main controls for the dashboard:

-   **Refresh:** Manually triggers a refresh of all data from the connected API servers. This is useful if you want to see the very latest data without waiting for the next automatic polling cycle.

-   **Columns (1, 2, 3, 4):** These buttons allow you to manually set the number of columns in the dashboard grid, overriding the automatic responsive layout. This is particularly useful for forcing a dense, multi-column layout on a high-resolution tablet. Your selection is saved automatically and will persist across browser sessions.

-   **Edit Layout:** This toggle button activates "Edit Mode," which unlocks the dashboard grid, allowing you to customize the position and settings of your modules. When active, modules will have a subtle highlight, and additional controls will become visible.

-   **Reset Dashboard:** This button will reset all of your dashboard customizations, including the module layout and any per-module settings, back to their default state.

-   **Settings:** This button opens the main settings panel, where you can manage global application preferences like server connections, appearance, and data retention policies.



### Interacting with Modules

Each module on the dashboard is a self-contained, interactive component that provides a specific piece of information.

-   **Expand/Collapse:** Click anywhere on a module's header to expand it for a detailed view or collapse it to save space. For example, expanding a CPU module will reveal a detailed historical chart, while the collapsed view shows a compact summary.

-   **Status Indicators:** The colored dot in the top-left of most modules indicates the current status of the metric being monitored. This color is not determined by the web interface itself but is passed directly from the `DashPad-API` instance, which is responsible for evaluating the data against predefined thresholds. This ensures that the status logic is centralized and consistent.


## Customizing the Layout

DashPad allows you to create a layout that is perfectly tailored to your needs. The drag-and-drop functionality makes it intuitive to organize your dashboard exactly how you want it.

1.  **Activate Edit Mode:** Click the **Edit Layout** button in the header bar. The dashboard will enter a state where you can move modules. You'll notice that the modules are now visually distinct, indicating they can be interacted with.

2.  **Drag and Drop:** Simply click and hold on any module's header and drag it to a new position. You can move modules within the same column or to a different column entirely. As you drag, a placeholder will show you where the module will be placed.

3.  **Save Layout:** Your changes are saved automatically and instantly to your browser's `localStorage` as you make them. There is no "save" button to press; your layout is always up-to-date.

4.  **Exit Edit Mode:** Click the **Edit Layout** button again to lock the layout in place and return to the normal viewing mode.


!!! tip "Plan Your Layout for Different Devices" 
    Since the layout configuration is stored in your browser's `localStorage`, it is unique to each device and browser. This allows you to create different layouts optimized for different screens. For example, you might have a dense 4-column layout on your desktop monitor but a simpler 2-column layout on a wall-mounted tablet.

## The Main Settings Panel

Clicking the global **Settings** (cog) icon in the header bar opens a panel with several configuration tabs, giving you control over the entire application's behavior.

-   **Server Management:** This tab lists all the API servers you configured with environment variables. Here, you can temporarily enable or disable polling for any server. This is useful if you need to take a server offline for maintenance and want to prevent connection errors from appearing on your dashboard, all without needing to restart the DashPad-Web container.

-   **Appearance Settings:** Customize the look and feel of your charts. You can change the default colors used for "Normal," "Warning," and "Critical" statuses. These color settings apply to both the detailed expanded charts and the subtle background sparklines, allowing you to create a theme that matches your preferences.

-   **Historical Data:** Configure the time windows for your charts. You can set the global default duration for expanded charts (e.g., 30 minutes) and the duration for the background sparklines (e.g., 10 minutes). These settings help you balance between viewing long-term trends and focusing on recent activity.

-   **Import/Export:** This powerful feature allows you to back up your entire UI configuration—including the module layout, server visibility states, and all global and module-specific settings—to a single JSON file. You can then use the "Import" function to restore this configuration on a different browser or device, making it easy to replicate your setup.


## Module-Specific Settings

You can override global settings for individual (applicable) modules to create a highly customized view. This allows for granular control over how data is presented.

!!! info "Accessing Module Settings" 

    There are two ways to access settings for a specific module:

    1. **General Method (Edit Mode):** First, enter **Edit Layout** mode. A new **cog icon** will appear in the header of each module. Clicking this will open its specific settings panel.
    2. **Metric Modules (CPU/RAM):** For modules that display charts, you can also click the **duration chip** (e.g., "30m") in the top-right corner of the expanded view to directly open the chart duration settings for that module.


### Log Module

-   **Autoscroll:** Toggle whether the log view automatically scrolls to the bottom as new lines arrive. This is useful for tailing live logs.

-   **Highlight Rules:** Define custom keywords (e.g., "error", "failed", "success") and assign specific colors to them. This makes it incredibly easy to spot important events in a fast-moving log stream.

### Metric Modules (CPU/RAM)

-   **Chart Duration:** Override the global chart duration to show more or less historical data for a specific metric. For example, you might want to see a 60-minute view for RAM usage but only a 15-minute view for CPU to focus on recent spikes.

### Alerts Module:

-   **Show Past Alerts:** Choose whether to display alerts that have already been resolved. This can be helpful for understanding recent system events.

-   **History Limit:** Set the maximum number of past alerts to keep in the view, preventing the list from becoming cluttered.
