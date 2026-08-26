# Command Snippets Library

> **Status:** VERIFIED (Phase 10 Implementation)  
> **Source Locations:** `src/modules/snippets/`, `src-tauri/src/db.rs` (Migration v6)

---

## Overview

The **Command Snippets Library** is a centralized store for frequently used terminal commands, deployment scripts, database queries, and CLI incantations. It supports categorization, favorite pinning, clipboard copying, and direct execution into active terminal panes.

---

## Problem It Solves

Developers waste significant time searching shell history, wikis, or notes for complex CLI commands (e.g. `docker exec -it <id> sh`, `git commit --amend --no-edit`, `ffmpeg` conversion strings). The Snippets module provides instant search, recall, and single-click execution directly in the active shell.

---

## Capabilities

- **Single-Click Shell Execution:** "Run in Terminal" types the single-line snippet directly into the focused terminal pane and switches to the Terminal module.
- **Categorization & Filtering:** Group snippets by tags/categories (e.g. `Git`, `Docker`, `Kubernetes`, `Database`).
- **Favorite Pinning:** Pin essential snippets to the top of the list for quick access.
- **Clipboard Integration:** Quick copy button with visual confirmation feedback.
- **Single-Line Safety Enforcement:** Commands are validated as single-line strings to prevent unintentional multi-command execution.

---

## User Workflow

1. Open the Snippets module via the left icon rail or `Ctrl+9`.
2. Click **"New Snippet"**, input the name, command string, description, and category.
3. Click **"Run"** to send the command directly to the active terminal pane, or **"Copy"** to copy to clipboard.

---

## Technical Implementation

- **Database (`src-tauri/src/db.rs`):**
  - Table: `snippets` (Migration v6).
  - Schema: `id INTEGER PRIMARY KEY AUTOINCREMENT`, `name TEXT UNIQUE COLLATE NOCASE`, `command TEXT NOT NULL`, `description TEXT DEFAULT ''`, `category TEXT DEFAULT ''`, `favorite INTEGER DEFAULT 0`, `created_at`, `updated_at`.
- **Terminal Insertion (`src/modules/snippets/services/snippet-service.ts`):**
  - Sends the raw command bytes to the active PTY session via `TerminalManager.pty_write` and updates the active module via `ui-store.ts`.

---

## Free / Pro Availability

- **Free Edition:** :white_check_mark: Full local snippet creation, editing, execution, and categorization.
- **Pro Edition:** Same local capabilities; reserved for upcoming cross-device snippet cloud sync.

---

## Limitations

- **Parameter Interpolation:** Dynamic placeholder substitution (e.g., prompting for `<container-id>` before execution) is not yet supported; commands execute as static text.

---

## Future Improvements

- [ ] Parameterized snippet variables with interactive input modals.
- [ ] Import/Export to standard Gist or JSON files.
