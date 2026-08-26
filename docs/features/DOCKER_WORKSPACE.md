# Docker Workspace & Container Dashboard

> **Status:** VERIFIED (Phase 7 & Phase 13 Implementation)  
> **Source Locations:** `src/modules/docker/`, `src-tauri/src/commands/docker/`

---

## Overview

The **Docker Workspace** is a high-performance container and service manager. It automatically groups containers by Docker Compose v2 projects, visualizes service dependencies using an interactive SVG graph, provides real-time streaming logs over Tauri Channels, maps published host ports, and launches container terminal shells directly inside the cockpit.

---

## Problem It Solves

Standard container tools like Docker Desktop consume substantial background memory and CPU resources. Developers often only need quick visibility into Compose services, live log streaming, container restarts, and published port maps without leaving their primary workspace.

---

## Capabilities

- **Docker Compose v2 Grouping:** Automatically groups running and stopped containers by Compose project (`com.docker.compose.project`), displaying service status, health checks, and restart states.
- **Topological Service Dependency Graph (`WorkspaceGraph.tsx`):** Pure SVG dependency graph displaying service hierarchy derived from `depends_on` labels, with hollow nodes representing defined but unstarted services.
- **Real-Time Streaming Log Drawer (`LogViewer.tsx`):** Sliding window log viewer streaming stdout/stderr over Tauri Channels with a 5,000-line circular buffer, batched ~80ms flushes, substring search, stream filtering, and auto-scroll.
- **Direct Container Shell Launch (`container-shell.ts`):** Opens an interactive `bash`/`sh` shell session inside the container as a new tab in the Modern Terminal module.
- **Whole-Project Actions:** Start, stop, and restart entire Compose stacks or individual containers with graceful confirmation guards.
- **Published Ports Dashboard (`PortsDashboard.tsx`):** Aggregate list of all host-bound container ports with one-click browser links.
- **Traditional Views:** Dedicated list views for Containers, Images, and Volumes.

---

## User Workflow

1. Open the Docker module via the left icon rail or `Ctrl+6`.
2. Inspect Compose workspace cards to view service health and dependencies.
3. Click the **Logs** button on any container to open the bottom streaming log drawer.
4. Click **"Open Shell"** to jump into an interactive container terminal.
5. Use project-level **Start / Stop / Restart** buttons to control multi-container stacks.

---

## Technical Implementation

- **Backend Handlers (`src-tauri/src/commands/docker/`):**
  - `compose.rs`: Probes Compose v2 projects (`docker compose ls -a --format json`) and service metadata.
  - `logs_stream.rs`: `DockerLogManager` spawns `docker logs -f --tail N` children, piping stdout/stderr across background threads to a Tauri `Channel<LogEvent>`.
  - `stats.rs`: Gathers CPU/memory utilization via non-streaming `docker stats`.
- **Frontend Grouping (`workspace-grouping.ts`):**
  - Parses Docker labels (`com.docker.compose.service`, `depends_on`, `working_dir`) and extracts health status strings.

---

## Free / Pro Availability

- **Free Edition:** :x: Not available (shows Pro upgrade banner).
- **Pro Edition:** :white_check_mark: Full access to Docker Workspaces, Compose graphs, streaming logs, container shells, and diagnostics.

---

## Limitations

- **Host Docker Engine Required:** Requires a local Docker CLI and running Docker daemon / WSL2 backend.
- **Windows Containers:** Direct shell launching defaults to `sh`/`bash`; Windows-based container images require manual `cmd.exe` invocation.

---

## Future Improvements

- [ ] Interactive volume file explorer.
- [ ] Container image vulnerability and size optimizer suggestions.
