# Workspace Manager & Layout Restore

> **Status:** VERIFIED (Phase 3 Implementation)  
> **Source Locations:** `src/modules/workspace/`, `src-tauri/src/db.rs` (Migration v2)

---

## Overview

The **Workspace Manager** enables developers to snapshot running multi-tab and split-pane terminal sessions—including tab structures, split ratios, active shell profiles, and per-pane working directories—into SQLite and restore them with a single click.

---

## Problem It Solves

Developers frequently work across multiple distinct projects or microservices that require specific terminal topologies (e.g. Tab 1: frontend + API split; Tab 2: database + worker split; Tab 3: Git logs). Recreating these layouts manually after every reboot or project switch wastes time and introduces friction.

---

## Capabilities

- **Session Snapshotting:** Captures the full recursive pane tree of every open tab into a named workspace.
- **One-Click Restore:** Reconstructs the saved tab layout, splits, shell profiles, and working directories from SQLite in fresh PTY sessions.
- **Overwriting & Updating:** "Update from current session" quickly syncs layout changes back to an existing workspace.
- **Custom Working Directories:** Per-pane directory assignment allowing tabs to open automatically in their respective project directories.
- **Conflict Warning:** Confirmation modal alerts the user if restoring a workspace will replace running terminal sessions.

---

## User Workflow

1. Arrange desired terminal tabs and split panes in the Terminal module.
2. Open the Workspaces module via the left icon rail or `Ctrl+2`.
3. Click **"Save current session"**, provide a workspace name (e.g., "E-Commerce Monorepo"), and confirm.
4. Later, click **"Open"** on the workspace card to rebuild the entire session and navigate directly to the Terminal.

---

## Technical Implementation

- **Database (`src-tauri/src/db.rs`):**
  - Table: `workspaces` (Migration v2).
  - Schema: `id INTEGER PRIMARY KEY AUTOINCREMENT`, `name TEXT UNIQUE COLLATE NOCASE`, `layout TEXT NOT NULL`, `created_at`, `updated_at`.
- **Layout Serialization:**
  - `workspace-service.ts` traverses the Zustand `pane-tree.ts` hierarchy, serializing pane IDs, split orientations, split ratios, profile keys, and `cwd` strings into a portable JSON document.
- **Session Rebuilding:**
  - Deserializes the JSON layout, resets active terminal sessions via `TerminalManager`, and invokes `pty_spawn` sequentially for each leaf pane.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available (shows Pro upgrade banner).
- **Pro Edition:** :white_check_mark: Full unlimited workspace creation, editing, snapshotting, and restoration.

---

## Limitations

- **Process State Persistence:** Restoring a workspace spawns fresh shell instances at the saved directories; it does not serialize active background processes (such as running node servers) that were terminated when the previous session closed.

---

## Future Improvements

- [ ] Automatic workspace auto-save on application exit.
- [ ] Exporting and sharing workspace configuration files (`.cockpit-workspace.json`) across teams.
