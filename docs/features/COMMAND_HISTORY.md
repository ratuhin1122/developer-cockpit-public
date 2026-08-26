# Command History & Command Palette

> **Status:** VERIFIED (Phase 1 & Phase 2 Implementation)  
> **Source Locations:** `src/components/command-palette.tsx`, `src/store/command-store.ts`, `src/modules/terminal/components/XtermPane.tsx`

---

## Overview

Developer Cockpit integrates two complementary command recall systems: the **Global Command Palette** (`Ctrl+K` / `F1`) for application-wide navigation and action dispatching, and **Shell-Native Command History** with xterm.js scrollback buffer management for terminal sessions.

---

## Problem It Solves

Keyboard-centric developers require rapid access to switch modules, restore workspaces, run snippets, or navigate open terminal tabs without taking their hands off the keyboard.

---

## Capabilities

- **Unified Command Palette (`Ctrl+K` or `F1`):**
  - Instant fuzzy search across all application modules.
  - Quick-switch across all open terminal tabs.
  - One-click execution of saved Workspaces and Projects.
  - Instant dispatching of Command Snippets into the active terminal.
- **Terminal Scrollback & Shell History:**
  - Up to 50,000 lines of scrollback history per terminal pane.
  - Native up/down arrow key command history handled transparently by underlying shells (`pwsh`, `cmd`, `bash`) via ConPTY.
  - In-buffer text search with match highlighting (`Ctrl+Shift+F`).

---

## Free / Pro Availability

- **Free Edition:** :white_check_mark: Full access to the Command Palette and Terminal scrollback history.
- **Pro Edition:** :white_check_mark: Full access, including Pro action indexing (Workspaces, Docker actions).

---

## Limitations

- The application does not log raw keystroke history into a global SQLite table for privacy and security reasons; command recall relies on shell-native history files (`PSReadLine`, `.bash_history`).
