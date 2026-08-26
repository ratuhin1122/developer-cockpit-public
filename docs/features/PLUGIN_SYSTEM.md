# Plugin System & Extensibility SDK

> **Status:** VERIFIED (Phase 11 & Phase 13 Implementation)  
> **Source Locations:** `src/modules/plugins/`, `sdk/`, `src-tauri/src/commands/plugins.rs`, `src-tauri/src/db.rs` (Migrations v7, v8)

---

## Overview

The **Plugin System** enables developers and enterprise teams to extend Developer Cockpit with custom sidebar modules, overview dashboard widgets, and workflow tools. Built around a dedicated TypeScript SDK (`@developer-cockpit/plugin-sdk`), plugins run inside sandboxed execution frames and interact with the host via a versioned `CockpitApi`.

---

## Problem It Solves

No single developer tool can anticipate every proprietary internal service, deployment portal, or specialized CLI workflow. The Plugin System allows developers to integrate custom tools directly into the Cockpit UI without modifying application source code.

---

## Capabilities

- **Folder-Based Plugins:** Simple directory structure (`plugin.json` + `index.js`) placed in `%APPDATA%\com.developercockpit.app\plugins\`.
- **TypeScript Plugin SDK (v2):** Strongly-typed development experience via `@developer-cockpit/plugin-sdk`.
- **Custom Sidebar Modules:** Register first-class navigation items appearing alongside built-in tools.
- **Overview Dashboard Panels:** Contribute custom metric cards and status widgets to the main Overview Dashboard.
- **Sandboxed Execution:** Executes in isolated `<iframe>` or `WebWorker` contexts communicating via `postMessage` RPC.
- **Scoped Key-Value Storage:** Isolated SQLite persistence per `plugin_id` backed by the `plugin_kv` database table.
- **User Trust & Enable Toggles:** Plugins discovered on disk are disabled by default until explicitly enabled by the user.

---

## User Workflow

1. Open the Plugins module via the left icon rail.
2. Click **"Open folder"** to reveal the plugins directory in File Explorer, or click **"Example plugin"** to scaffold a working `hello-world` plugin.
3. Toggle the enable switch on any discovered plugin to load and mount it immediately without restarting the application.

---

## Technical Implementation

- **Manifest Specification (`plugin.json`):**
  - Properties: `id`, `name`, `version`, `description`, `icon`, `api: 2`, `minAppVersion`.
- **Host Sandbox (`plugin-sandbox.ts`):**
  - Renders a hidden sandboxed frame and sets up bidirectional message dispatching.
- **Database (`src-tauri/src/db.rs`):**
  - Table: `plugin_settings` (Migration v7) storing enabled/disabled state.
  - Table: `plugin_kv` (Migration v8) storing isolated key-value pairs per plugin.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available (shows Pro upgrade banner).
- **Pro Edition:** :white_check_mark: Full plugin development, loading, and execution support.

---

## Limitations

- **No Raw OS Shell Access:** For security, plugins cannot execute arbitrary native shell scripts directly without user confirmation or through the standard Terminal API.

---

## Future Improvements

- [ ] Official public plugin marketplace registry with one-click online installation.
- [ ] Plugin permissions manifest prompting users for specific capability access.
