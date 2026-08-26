# Project Launcher & Multi-Step Orchestrator

> **Status:** VERIFIED (Phase 4 Implementation)  
> **Source Locations:** `src/modules/projects/`, `src-tauri/src/commands/launcher.rs`, `src-tauri/src/commands/projects.rs`, `src-tauri/src/db.rs` (Migration v3)

---

## Overview

The **Project Launcher** automates end-to-end development environment startup. It allows developers to define named projects consisting of ordered launch steps that start IDEs, open terminal tabs with pre-typed build commands, launch local URLs in browsers, and open project directories in File Explorer with a single click.

---

## Problem It Solves

Starting work on a project often requires 4–6 manual repetitive steps: opening VS Code in a folder, opening a terminal to run `npm run dev`, opening a second terminal to run Docker Compose, and opening a browser tab to `http://localhost:3000`. The Project Launcher reduces this routine to a single automated action.

---

## Capabilities

- **Multi-Step Execution Pipeline:**
  1. **Open App:** Spawns detached GUI executables (e.g. `code .`, Docker Desktop, Visual Studio) with custom arguments.
  2. **Run in Terminal:** Opens a new terminal tab at a designated folder and sends an initial command string (e.g., `npm run dev`, `cargo watch -x run`).
  3. **Open URL:** Opens designated URLs (e.g. `http://localhost:5173`) in the default system browser via `@tauri-apps/plugin-opener`.
  4. **Open Folder:** Opens Windows File Explorer at the project path.
- **Framework Auto-Detectors (`detectors.ts`):** Automatically scans imported project directories and proposes pre-configured launch steps for:
  - Node.js (`package.json` scripts: `dev`, `start`)
  - Rust (`Cargo.toml`)
  - Go (`go.mod`)
  - Python (`requirements.txt`, `pyproject.toml`, `Pipfile`)
  - Docker (`docker-compose.yml`, `compose.yaml`)
- **Fault-Tolerant Execution:** If one step fails (e.g., an optional tool is not on `PATH`), subsequent launch steps continue executing while presenting non-blocking warning feedback.
- **Recent Projects Panel:** Quick-launch card widget displayed on the main Overview Dashboard.

---

## User Workflow

1. Open the Projects module via the left icon rail or `Ctrl+3`.
2. Click **"New Project"** or **"Import Folder"** (which automatically runs framework detectors).
3. Reorder or customize launch steps (App, Terminal, URL, Folder).
4. Click **"Launch"** to execute all steps sequentially.

---

## Technical Implementation

- **Database (`src-tauri/src/db.rs`):**
  - Table: `projects` (Migration v3).
  - Schema: `id INTEGER PRIMARY KEY AUTOINCREMENT`, `name TEXT UNIQUE COLLATE NOCASE`, `config TEXT NOT NULL`, `created_at`, `updated_at`.
- **Backend Launcher (`src-tauri/src/commands/launcher.rs`):**
  - `launch_program(program, args, cwd)`: Spawns detached processes without locking the Tauri event loop, utilizing `std::process::Command` with Windows creation flags.

---

## Free / Pro Availability

- **Free Edition:** :white_check_mark: Single-step program launch.
- **Pro Edition:** :white_check_mark: Full multi-step automated execution pipelines.

---

## Limitations

- **Process Supervision:** Launched external applications (like VS Code) run detached; Developer Cockpit does not track when external GUI applications are closed.

---

## Future Improvements

- [ ] Conditional step execution (e.g. only open browser once the local port is actively listening).
- [ ] Environment variable injection per project launch profile.
