# Developer Cockpit — Feature Specifications

This directory contains in-depth, verified feature specifications and technical documentation for Developer Cockpit.

---

## 📊 [Complete Feature Matrix](./FEATURE_MATRIX.md)
*Overview table detailing all capabilities, Free vs. Pro availability, and implementation statuses.*

---

## Core Feature Specifications

1. **[Modern Terminal & Multi-Pane Shell](./TERMINAL.md)**  
   *ConPTY pseudo-console, multi-tab sessions, split panes, shell selection, and font zoom.*

2. **[Terminal Settings & Themes Engine](./TERMINAL_SETTINGS.md)**  
   *Live-updating settings, 6 curated themes, font family selection, and cursor styling.*

3. **[Workspace Manager & Layout Restore](./WORKSPACES.md)**  
   *Snapshotting live terminal layouts and split trees into SQLite with 1-click restore.*

4. **[Project Launcher & Multi-Step Orchestrator](./PROJECT_LAUNCHER.md)**  
   *Multi-step automated execution pipelines, framework detectors, and recent projects.*

5. **[Port Manager & Process Inspector](./PORT_MANAGER.md)**  
   *Real-time socket discovery, Win32 process memory inspection, tree killing, and restart.*

6. **[Git Dashboard & Advanced Version Control](./GIT_DASHBOARD.md)**  
   *Repository tracking, SVG commit graph, stashes, tags, merge/cherry-pick assistants, analytics.*

7. **[Docker Workspace & Container Dashboard](./DOCKER_WORKSPACE.md)**  
   *Compose v2 grouping, SVG dependency graph, streaming logs drawer, and container terminal shells.*

8. **[Docker Doctor & Environment Diagnostics](./DOCKER_DOCTOR.md)**  
   *WSL2 UTF-16LE health checks, reclaimable disk space, memory limits, and port collision detection.*

9. **[SSH Profile Manager & Remote Shells](./SSH_MANAGER.md)**  
   *Connection profile organizer, zero-password architecture, and direct terminal shell launch.*

10. **[Command Snippets Library](./COMMAND_SNIPPETS.md)**  
    *Categorized command repository with single-click execution into active terminal shells.*

11. **[Version Dashboard & Toolchain Inspector](./VERSION_DASHBOARD.md)**  
    *Auto-detection, version extraction, and binary path resolution for 12 developer tools.*

12. **[Plugin System & Extensibility SDK](./PLUGIN_SYSTEM.md)**  
    *`@developer-cockpit/plugin-sdk`, sandboxed iframe/worker runtime, manifest format, and scoped storage.*

13. **[Command History & Command Palette](./COMMAND_HISTORY.md)**  
    *Global `Ctrl+K` command palette and xterm.js scrollback buffer management.*

14. **[Free vs. Pro Editions & Capability Gating](./EDITIONS_FREE_PRO.md)**  
    *Edition boundaries, feature catalog gates, and upgrade experience.*
