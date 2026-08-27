# Developer Cockpit — Product Demonstrations & Video Walkthroughs

This document collects live video walkthroughs and step-by-step interactive demonstration scripts for Developer Cockpit.

---

## 1. Live Video Demonstrations

Watch live video demonstrations of Developer Cockpit in action:

| Module / Feature Area | Video Demonstration | Key Capabilities Showcased |
| :--- | :--- | :--- |
| **Modern Terminal** | [🎬 Watch Terminal Demo](https://youtu.be/pbQiyEtL-kg) | ConPTY integration, multi-tab sessions, split panes, shell selection (PowerShell 7, CMD, Git Bash), custom themes, and zoom controls. |
| **Project & Workspace Launcher** | [🎬 Watch Launcher & Workspace Demo](https://youtu.be/T-7YOJxmpxM) | One-click multi-step launch workflows (IDE, dev servers, browser) and instant terminal session snapshot & restore. |
| **Port Manager, Versions & Snippets** | [🎬 Watch Ports, Versions & Snippets Demo](https://youtu.be/LAAn1ov8Zik) | Real-time TCP socket detection, process command line recovery, graceful/forceful process tree termination, 12-toolchain version detector, and snippet insertion. |
| **Git Dashboard** | [🎬 Watch Git Dashboard Demo](https://youtu.be/KyMxAcBrQBM) | Visual SVG commit graph, branch management, ahead/behind tracking, stash manager, tag manager, and cherry-pick/merge assistants. |
| **Docker Workspace & Doctor** | [🎬 Watch Docker Workspace Demo](https://youtu.be/VJgx_NyVtsk) | Compose v2 service grouping, dependency graph, streaming log drawer, container terminal shell, and WSL2 Docker Doctor checks. |
| **Plugin System (SDK v2)** | [🎬 Watch Plugin System Demo](https://youtu.be/Mu6EPB-zN7g) | Extensible TypeScript SDK, sandboxed execution, manifest validation, and scoped key-value storage. |

---

## 2. Interactive Feature Walkthroughs

### 2.1 Terminal & Workspace Workflow
1. Open multiple terminal tabs running PowerShell 7, Command Prompt, or Git Bash.
2. Split panes horizontally or vertically with flexible resizing.
3. Save the entire session as a named Workspace ("Fullstack Dev").
4. Close the application, restart, and restore the exact layout with a single click.

### 2.2 Project Launcher Workflow
1. Create a Project entry ("E-Commerce Platform").
2. Configure automated launch steps:
   - Launch IDE (`code .`).
   - Open Terminal Pane 1 and run `npm run dev`.
   - Open Terminal Pane 2 and run `docker compose up`.
   - Launch browser at `http://localhost:3000`.
3. Execute the full project stack with one click.

### 2.3 Port Manager & Process Termination
1. Open the Port Manager to view all active listening TCP ports on the machine.
2. Inspect PID, local address, and exact process command line arguments.
3. Terminate runaway or orphaned dev servers with recursive process tree killing, or restart them in place.

### 2.4 Docker Workspace & Diagnostics
1. View Compose v2 services automatically grouped with health indicators and state dots.
2. View the visual SVG service dependency graph.
3. Open the streaming log drawer to view real-time container output.
4. Run Docker Doctor to verify WSL2 status, reclaimable disk space, and daemon health.

### 2.5 Advanced Git Workflow
1. Track local repositories.
2. View branch status, ahead/behind upstream counts, and staged/unstaged changes.
3. Navigate the visual SVG commit history graph.
4. Manage stashes, create/delete tags, and use the merge/cherry-pick conflict assistants.

---

## 3. Visual Assets

Screenshots and architectural diagrams are located in the [`assets/`](../assets/) directory.
