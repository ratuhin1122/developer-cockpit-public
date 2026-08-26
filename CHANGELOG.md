# Changelog

All notable changes to Developer Cockpit will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-08-26

### Added
- **Core Architecture:** Tauri v2 native desktop shell with React 19, TypeScript 5.8, Tailwind CSS v4, and SQLite database backend.
- **Modern Terminal Module:** ConPTY-backed multi-tab, split-pane terminal powered by xterm.js 6 with custom profiles and themes.
- **Workspace Manager:** Snapshotting and restoring of live terminal sessions and split layouts into SQLite.
- **Project Launcher:** Orchestrated multi-step project launches (IDEs, terminal commands, URLs, folders) with project type auto-detection.
- **Port Manager:** Live socket monitoring via `netstat`, process command line inspection, process tree termination, and restart.
- **Git Dashboard:** Visual SVG commit graph, branch management, ahead/behind tracking, stash manager, tag manager, cherry-pick/merge assistants, and repository analytics.
- **Docker Workspace:** Compose v2 grouping, live service dependency graph, real-time log streaming over Tauri Channels, and WSL2 Docker Doctor health checks.
- **SSH Manager:** Profile management with direct terminal launching and zero password storage policy.
- **Version Dashboard:** Automatic discovery and version verification for 12 core development toolchains.
- **Command Snippets Library:** Categorized snippet management with one-click insertion into active terminal shells.
- **Settings Hub:** Centralized configuration across 13 dedicated sections with automatic persistence.
- **Plugin System & SDK v2:** Extensible `@developer-cockpit/plugin-sdk`, sandboxed iframe/worker execution, manifest validation, and scoped key-value storage.
- **Licensing Engine:** Cryptographic Ed25519 offline token validation (`DCK.v1`), Windows DPAPI encrypted token storage, and 30-day offline grace period evaluator.
