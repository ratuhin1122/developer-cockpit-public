# Docker Doctor & Environment Diagnostics

> **Status:** VERIFIED (Phase 13 Implementation)  
> **Source Locations:** `src/modules/docker/components/DoctorView.tsx`, `src-tauri/src/commands/docker/doctor.rs`

---

## Overview

**Docker Doctor** is an automated environment diagnostic utility built into the Docker Workspace module. It verifies container subsystem health, detects common configuration bottlenecks on Windows, evaluates WSL2 memory limits, identifies port allocation conflicts, and provides copyable remediation commands.

---

## Problem It Solves

Docker setups on Windows often suffer from complex underlying failure modes: WSL2 daemon unresponsiveness, excessive virtual disk growth (`ext4.vhdx`), port collisions with host processes, or misconfigured WSL memory allocations. Docker Doctor surfaces these issues in a single checklist with one-click fixes.

---

## Capabilities

- **CLI & Daemon Validation:** Verifies that `docker.exe` is reachable on `PATH` and that the daemon API answers commands.
- **Compose v2 Detection:** Ensures modern Compose v2 CLI plugins are installed.
- **Container Mode Check:** Confirms the daemon is operating in Linux-containers mode rather than legacy Windows-containers mode.
- **WSL 2 Health & UTF-16LE Decoding:** Probes `wsl.exe --status`, decoding the native wide-character output to check default WSL distribution and kernel versions.
- **Reclaimable Disk Space (`docker system df`):** Calculates reclaimable build cache, unused images, and stopped container layers, warning when reclaimable space exceeds 5 GB.
- **WSL2 Memory Optimization Tip:** Detects host RAM and suggests optimal `.wslconfig` memory limits for developer workstations.
- **Port Conflict Detector:** Correlates published ports of *stopped* containers against active listening host ports to warn before startup failures occur.

---

## User Workflow

1. Open the Docker module and switch to the **"Doctor"** tab.
2. Review the diagnostic checklist (Pass, Warn, Fail, Info).
3. For any warning or failure, click the copy button next to the suggested remediation command (e.g. `docker system prune -a --volumes` or `.wslconfig` snippet).

---

## Technical Implementation

- **Backend Handler (`src-tauri/src/commands/docker/doctor.rs`):**
  - Executes system diagnostic commands with `CREATE_NO_WINDOW`.
  - Implements custom UTF-16LE byte conversion to parse `wsl.exe --status` output on Windows without encoding corruptions.
  - Returns structured `DoctorCheck` objects: `{ id, title, status, detail, fix }`.
- **Frontend Engine (`DoctorView.tsx`):**
  - Combines backend results with active port state from `port-store.ts` to surface pre-emptive collision warnings.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available.
- **Pro Edition:** :white_check_mark: Full access to Docker Doctor diagnostics.

---

## Limitations

- **Remediation Commands:** Fixes are provided as copyable commands for safety; Docker Doctor never executes destructive cleanup commands automatically without user action.

---

## Future Improvements

- [ ] Automated `.wslconfig` generator wizard.
- [ ] Disk compaction helper for `ext4.vhdx` virtual hard disks.
