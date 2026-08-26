# Port Manager & Process Inspector

> **Status:** VERIFIED (Phase 5 Implementation)  
> **Source Locations:** `src/modules/ports/`, `src-tauri/src/commands/ports/`

---

## Overview

The **Port Manager** provides a real-time table of all listening TCP/TCPv6 sockets on the host system. It combines low-level Windows networking queries with direct Win32 process memory inspection to identify the exact command-line arguments and working directories of listening processes, enabling one-click browser testing, graceful/forceful process tree termination, and in-place process restart.

---

## Problem It Solves

Developers frequently encounter `EADDRINUSE: address already in use :::3000` errors caused by orphaned dev servers or background processes. Traditional tools require hunting for PIDs in Task Manager or running multiple `netstat` and `taskkill` commands in a terminal without knowing which specific project directory owns the port.

---

## Capabilities

- **Live Listening Socket Discovery:** Identifies listening ports, process names, PIDs, and local bind addresses (`0.0.0.0`, `127.0.0.1`, `::`).
- **Deep Process Introspection:** Recovers the full command line arguments and original working directory (`cwd`) of the process using Win32 memory traversal.
- **Process Tree Termination:** Force-kills the process **and all its spawned children** (crucial for Node/npm/Python processes that spawn child build workers).
- **In-Place Process Restart:** Reads the recovered command line and working directory, terminates the running instance, and relaunches it detached.
- **Quick Browser Launch:** One-click button to open `http://localhost:<port>` in the default browser.
- **Configurable Auto-Refresh:** Opt-in background polling (2s–120s interval) while the module is active.

---

## User Workflow

1. Open the Port Manager module via the left icon rail or `Ctrl+4`.
2. Filter the table by port number (e.g. `3000`) or process name (e.g. `node`).
3. View the process name, PID, and working directory.
4. Click **"Kill"** to free the port or **"Restart"** to reboot the dev server in place.

---

## Technical Implementation

- **Socket Scanning (`src-tauri/src/commands/ports/windows.rs`):**
  - Executes `netstat -ano -p TCP` and `netstat -ano -p TCPv6` with `CREATE_NO_WINDOW`.
  - Filters strictly for `LISTENING` socket states and parses PIDs and addresses.
- **Win32 Memory Inspection:**
  - Calls `NtQueryInformationProcess` to retrieve the `ProcessEnvironmentBlock` (PEB).
  - Uses `ReadProcessMemory` to read `RTL_USER_PROCESS_PARAMETERS` (`CommandLine` and `CurrentDirectory`).
- **Process Tree Killing:**
  - Uses `taskkill /F /T /PID <pid>` to terminate entire process subtrees.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available (shows Pro upgrade banner).
- **Pro Edition:** :white_check_mark: Full socket inspection, process memory recovery, tree termination, and restart.

---

## Limitations

- **System / Elevated Processes:** Processes owned by `SYSTEM` or other Windows user accounts cannot have their memory read or be terminated without running Developer Cockpit as Administrator.

---

## Future Improvements

- [ ] Support for UDP socket listening detection.
- [ ] Port reservation notifications alerting developers when a background service silently claims a common port.
