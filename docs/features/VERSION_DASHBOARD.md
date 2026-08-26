# Version Dashboard & Toolchain Inspector

> **Status:** VERIFIED (Phase 8 Implementation)  
> **Source Locations:** `src/modules/versions/`, `src-tauri/src/commands/versions.rs`

---

## Overview

The **Version Dashboard** automatically discovers, probes, and displays installation status, version numbers, and binary execution paths for 12 major developer toolchains and runtimes installed on the host system.

---

## Problem It Solves

When setting up new workstations, onboarding team members, or debugging environment discrepancies ("it works on my machine"), verifying installed compilers, package managers, and runtimes normally requires running a dozen separate terminal `--version` and `where` commands.

---

## Capabilities

- **12 Toolchain Auto-Detection:** Automatically probes:
  1. **Node.js** (`node --version`)
  2. **npm** (`npm --version`)
  3. **pnpm** (`pnpm --version`)
  4. **Yarn** (`yarn --version`)
  5. **Bun** (`bun --version`)
  6. **Python** (`python --version`)
  7. **Git** (`git --version`)
  8. **Rust** (`rustc --version`)
  9. **Cargo** (`cargo --version`)
  10. **Go** (`go version`)
  11. **Java** (`java -version`)
  12. **Docker** (`docker --version`)
- **Status Indicators:** Clear green (installed) / gray (missing) indicators with summary badge (e.g., "8 of 12 known tools installed").
- **Binary Path Resolution:** Extracts the primary execution path using Windows `where.exe` with a one-click copy button.
- **Full Version Tooltips:** Hovering over the parsed version string reveals the tool's raw multi-line compiler banner.

---

## User Workflow

1. Open the Versions module via the left icon rail or `Ctrl+7`.
2. Review the list of detected tools and installed versions.
3. Click the copy icon next to any tool's binary path to copy it to the clipboard.
4. Click **"Refresh"** to force a live re-probe of the system `PATH`.

---

## Technical Implementation

- **Backend Probing (`src-tauri/src/commands/versions.rs`):**
  - Executes `--version` commands using `std::process::Command` with `CREATE_NO_WINDOW`.
  - Parses version numbers via regular expressions tailored to each tool's output syntax (e.g. stripping `v` prefixes, parsing Java stderr output).
  - Queries `where.exe <tool>` to retrieve absolute binary filesystem locations.

---

## Free / Pro Availability

- **Free Edition:** :white_check_mark: Full access to the Version Dashboard.
- **Pro Edition:** :white_check_mark: Full access.

---

## Limitations

- **Read-Only:** The Version Dashboard inspects installed tools; it does not install, update, or switch toolchain versions (like `nvm` or `rustup`).

---

## Future Improvements

- [ ] Support for detecting version managers (e.g., `nvm`, `pyenv`, `asdf`, `sdkman`).
- [ ] Environment variable (`PATH`) analyzer to detect binary shadowing conflicts.
