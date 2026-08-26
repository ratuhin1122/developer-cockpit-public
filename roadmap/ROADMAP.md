# Developer Cockpit — Product Roadmap

This document outlines the strategic architectural and functional roadmap for Developer Cockpit.

---

## Phase 1: Native Windows Core (v0.1.0 — Completed)
- [x] Tauri v2 + React 19 + Rust + SQLite foundation
- [x] Multi-tab & split-pane Modern Terminal with ConPTY integration
- [x] Workspace snapshot and restore
- [x] Multi-step Project Launcher with framework detection
- [x] Win32 Port Manager with process tree termination
- [x] Git Dashboard with SVG commit graph, merge/cherry-pick assistants, analytics
- [x] Docker Workspace with Compose v2 grouping, channel log streaming, Docker Doctor
- [x] Zero-password SSH Profile Manager
- [x] Version Dashboard detecting 12 developer runtimes
- [x] Command Snippets Library
- [x] Plugin SDK v2 with sandboxed execution and scoped storage
- [x] Offline Ed25519 licensing with Windows DPAPI encryption and 30-day grace window

---

## Phase 2: Extensibility & Ecosystem (In Progress / Planned)
- [ ] **Remote Plugin Registry:** Official community and enterprise plugin repository index.
- [ ] **Expanded Plugin Contributions:** Additional hook points for custom toolbars, status bar items, and custom file viewer panels.
- [ ] **Cross-Workspace Context Sharing:** Enhanced project metadata extraction for IDE extensions.

---

## Phase 3: Cloud Synchronization & Collaboration (Future)
- [ ] **Cloud Sync Backend:** End-to-end encrypted synchronization for settings, snippets, and workspaces across devices.
- [ ] **Team Workspaces:** Shared project configurations and team-wide launch profiles.
- [ ] **Enterprise License Server Integration:** Automated activation and team license management dashboard.

---

## Phase 4: Multi-Platform Exploration (Long-Term)
- [ ] **POSIX PTY & Platform Abstraction:** Abstract platform-specific Win32/DPAPI/ConPTY code to support macOS and Linux desktop environments.
