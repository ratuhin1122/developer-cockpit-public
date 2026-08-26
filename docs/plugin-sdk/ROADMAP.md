# Plugin SDK & Extensibility Roadmap

> **Status:** Roadmap & Future Capabilities Guide

---

## 1. Capability Maturity Matrix

| Extensibility Capability | Current Status | Description |
| :--- | :---: | :--- |
| **Custom Sidebar Modules** | **VERIFIED (v2)** | Full-page sandboxed views mounted from plugins. |
| **Dashboard Metric Cards** | **VERIFIED (v2)** | Contributed widgets on the Overview Dashboard grid. |
| **Command Palette Actions** | **VERIFIED (v2)** | Custom actions searchable in `Ctrl+K`. |
| **Terminal Command Sugar** | **VERIFIED (v2)** | Declarative terminal launcher actions without JS logic. |
| **Context Menu Extensions** | **VERIFIED (v2)** | Right-click extensions on Docker containers and Project cards. |
| **Settings Sub-Pages** | **VERIFIED (v2)** | Dedicated configuration views under Settings. |
| **Project Import Detectors** | **VERIFIED (v2)** | Custom folder heuristics during project import. |
| **Scoped SQLite KV Storage** | **VERIFIED (v2)** | Isolated persistent key-value store per plugin. |
| **Folder & ZIP Installation** | **VERIFIED (v2)** | Native file pickers for local folder/ZIP installs. |
| **Online Plugin Marketplace** | **PLANNED** | Centralized community registry with 1-click cloud install. |
| **Granular Permission Manifests** | **PLANNED** | Explicit capability requests (`network`, `storage`, `terminal`). |
| **Custom Status Bar Items** | **PLANNED** | Plugin-contributed status bar widgets. |
| **Custom File Previews** | **PLANNED** | Specialized file viewers for logs, CSVs, or diagrams. |

---

## 2. Planned SDK Improvements

### 2.1 Online Plugin Marketplace
A cloud-hosted plugin registry allowing community authors to publish packages via npm or git tags, enabling users to search, review, and install verified plugins directly from within the Cockpit UI.

### 2.2 Granular Permissions Model
Introducing a `permissions` array in `plugin.json` (e.g. `"permissions": ["storage", "notifications", "terminal"]`) that prompts users during installation for explicit capability authorization.
