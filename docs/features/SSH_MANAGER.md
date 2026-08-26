# SSH Profile Manager & Remote Shells

> **Status:** VERIFIED (Phase 9 Implementation)  
> **Source Locations:** `src/modules/ssh/`, `src-tauri/src/commands/ssh.rs`, `src-tauri/src/db.rs` (Migration v5)

---

## Overview

The **SSH Manager** provides a secure profile organizer for remote server connections. It allows developers to store host connection metadata, categorize profiles by groups, star favorite hosts, and launch live SSH sessions inside the Modern Terminal with a single click.

---

## Problem It Solves

Managing multiple remote hosts (development servers, staging environments, staging databases) via command lines or complex GUI tools often results in credential sprawl, insecure plaintext password storage, or cumbersome terminal switching.

---

## Capabilities

- **Profile Management:** Store host names, IP addresses, custom ports (default 22), usernames, and identity key file paths (`~/.ssh/id_rsa`, `~/.ssh/id_ed25519`).
- **Group Organization & Favorites:** Organize connections by environment or project groups with starred favorites sorted to the top.
- **Direct Terminal Integration:** Clicking **"Connect"** opens a dedicated tab in the Modern Terminal running `ssh -p <port> <user>@<host>` (with `-i <identity_file>` if specified) and switches directly to the terminal view.
- **Zero-Password Storage Security Policy:** **Passwords are never collected or stored in SQLite.** Authentication is handled securely via SSH keys, the system SSH Agent, or interactive password prompts inside the live terminal.
- **OpenSSH Detection:** Verifies that a valid OpenSSH client binary (`ssh.exe`) exists on `PATH`, displaying an amber setup guide if missing.

---

## User Workflow

1. Open the SSH module via the left icon rail or `Ctrl+8`.
2. Click **"New Profile"** and input host address, port, username, and identity key file.
3. Organize into a group (e.g., "Staging Cluster") and star if used frequently.
4. Click **"Connect"** on any profile card to immediately open an authenticated remote terminal session.

---

## Technical Implementation

- **Database (`src-tauri/src/db.rs`):**
  - Table: `ssh_profiles` (Migration v5).
  - Schema: `id INTEGER PRIMARY KEY AUTOINCREMENT`, `name TEXT UNIQUE COLLATE NOCASE`, `host TEXT NOT NULL`, `port INTEGER DEFAULT 22`, `username TEXT DEFAULT ''`, `identity_file TEXT DEFAULT ''`, `group_name TEXT DEFAULT ''`, `favorite INTEGER DEFAULT 0`, `created_at`, `updated_at`.
- **Backend Check (`src-tauri/src/commands/ssh.rs`):**
  - `ssh_available`: Probes for `ssh.exe` on system `PATH`.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available (shows Pro upgrade banner).
- **Pro Edition:** :white_check_mark: Full SSH profile management and terminal launch integration.

---

## Limitations

- **No Built-in SFTP Browser:** The module manages shell connections; SFTP/SCP graphical file browsing is not currently implemented.

---

## Future Improvements

- [ ] Automatic import from local `~/.ssh/config` files.
- [ ] SSH tunnel / port-forwarding configuration manager.
