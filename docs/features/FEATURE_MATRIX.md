# Developer Cockpit — Complete Feature Matrix

> **Source of Truth:** Verified against Developer Cockpit v0.1.0 codebase.

---

## Complete Feature Matrix

| Feature | Free | Pro | Status | Implementation Notes |
| :--- | :---: | :---: | :---: | :--- |
| **Modern Terminal** | :white_check_mark: | :white_check_mark: | **VERIFIED** | ConPTY + xterm.js 6, unlimited tabs, horizontal & vertical split panes. |
| **Terminal Shell Selection** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Auto-detects PowerShell 7, Windows PowerShell, CMD, Git Bash, WSL. |
| **Terminal Themes & Sizing** | :white_check_mark: | :white_check_mark: | **VERIFIED** | 6 curated themes (Dracula, Nord, etc.), dynamic zoom (`Ctrl +/-/0`), cursor customization. |
| **In-Terminal Search** | :white_check_mark: | :white_check_mark: | **VERIFIED** | `@xterm/addon-search` forward/backward search with match highlighting. |
| **Command Palette (`Ctrl+K`)** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Fuzzy action and navigation search across all modules, tabs, and snippets. |
| **Version Dashboard** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Probes 12 developer tools, displays version string, path, and copy actions. |
| **Command Snippets Library** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Categorized snippet library with one-click insertion into active terminal. |
| **Project Launcher (Single-Step)** | :white_check_mark: | :white_check_mark: | **VERIFIED** | One-click single executable or folder launching. |
| **Basic Git Dashboard** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Track repos, branch status, ahead/behind upstream counts, changed file status. |
| **Settings Hub (13 Sections)** | :white_check_mark: | :white_check_mark: | **VERIFIED** | Full persistence in SQLite for general, appearance, terminal, and module settings. |
| **Workspace Manager** | :x: | :white_check_mark: | **VERIFIED** | Snapshot live multi-tab/split terminal layouts into SQLite and restore with 1-click. |
| **Project Launcher (Multi-Step)** | :x: | :white_check_mark: | **VERIFIED** | Multi-action launch sequences (App + Terminal Script + URL + Folder) with framework auto-detectors. |
| **Port Manager** | :x: | :white_check_mark: | **VERIFIED** | Live TCP socket list, Win32 process memory inspection, process tree killing, restart. |
| **Advanced Git Commit Graph** | :x: | :white_check_mark: | **VERIFIED** | Pure SVG multi-branch topological commit history graph. |
| **Git Stash & Tag Managers** | :x: | :white_check_mark: | **VERIFIED** | Create, apply, pop, drop stashes; view, create, delete tags. |
| **Git Merge & Cherry-Pick Assistants** | :x: | :white_check_mark: | **VERIFIED** | Guided merge and cherry-pick flows with conflict guidance. |
| **Git Contributor Analytics** | :x: | :white_check_mark: | **VERIFIED** | Aggregates commit velocity and author distribution metrics. |
| **Docker Workspace & Compose Grouping** | :x: | :white_check_mark: | **VERIFIED** | Compose v2 grouping, service health badges, project-wide actions. |
| **Docker Dependency Graph** | :x: | :white_check_mark: | **VERIFIED** | Pure SVG service dependency graph based on Compose `depends_on` labels. |
| **Docker Live Log Drawer** | :x: | :white_check_mark: | **VERIFIED** | Tauri Channel streaming, 5,000-line circular buffer, search, stream filters. |
| **Docker Container Terminal Shell** | :x: | :white_check_mark: | **VERIFIED** | One-click `sh`/`bash` terminal launch inside containers. |
| **Docker Doctor & WSL2 Diagnostics** | :x: | :white_check_mark: | **VERIFIED** | WSL2 UTF-16LE status decoding, reclaimable disk space, memory limits, port collisions. |
| **SSH Profile Manager** | :x: | :white_check_mark: | **VERIFIED** | Zero-password architecture, connection grouping, direct terminal launch. |
| **Plugin System & SDK (v2)** | :x: | :white_check_mark: | **VERIFIED** | `@developer-cockpit/plugin-sdk`, sandboxed execution, custom modules, scoped KV storage. |
| **Offline Cryptographic Licensing** | :x: | :white_check_mark: | **VERIFIED** | `DCK.v1` Ed25519 digital signatures, Windows DPAPI encryption, 30-day offline grace window. |
| **Snippet Cloud Sync** | :x: | :white_check_mark: | **PLANNED** | Reserved in feature catalog (`snippets-sync`). |
| **Settings Cloud Sync** | :x: | :white_check_mark: | **PLANNED** | Reserved in feature catalog (`cloud-sync`). |
| **Online Activation Server Sync** | :x: | :white_check_mark: | **PARTIALLY VERIFIED** | Background sync task implemented in Rust; server endpoint URL (`VERIFY_URL`) currently empty pending server deployment. |
| **Automated Frontend Test Suite** | — | — | **NOT IMPLEMENTED** | Frontend unit/E2E test runners (Vitest/Playwright) not yet integrated in `package.json`. |
