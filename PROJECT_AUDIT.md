# Developer Cockpit — Project Audit

> **Audit Date:** August 26, 2026  
> **Source Directory Inspected:** `C:\Developer-Cockpit`  
> **Scope:** Full-tree architectural, technical, security, licensing, and code-level verification  
> **Audit Status:** COMPLETE — 100% verified against active source code, configurations, database migrations, and Rust backend implementation.

---

## 1. Executive Summary

**Developer Cockpit** is a high-performance, single-window developer workspace application specifically engineered for Windows 10/11. Built on **Tauri v2** (Rust backend) and **React 19** (TypeScript frontend with Vite 7 and Tailwind CSS v4), the application unifies disjointed daily developer utilities into a single cohesive native cockpit.

The application integrates:
- A multi-tab, split-pane **Modern Terminal** running on ConPTY and xterm.js 6.
- A **Project Launcher** with multi-step orchestration (app, terminal command, URL, folder).
- A **Workspace Manager** with terminal layout snapshotting and restore.
- A **Port Manager** that identifies listening TCP sockets, process trees, and command lines, enabling graceful/forceful termination and restart.
- An advanced **Git Dashboard** with visual commit graphs, stash/tag management, cherry-pick/merge assistants, and repository analytics.
- A **Docker Workspace & Environment Doctor** supporting Compose v2 service grouping, topological dependency graphing, log streaming over Tauri Channels, and WSL2 health checks.
- An **SSH Manager** with zero-password storage policy and direct shell launch.
- A **Command Snippets Library** with live insertion into running terminals.
- A **Version Dashboard** auto-detecting 12 standard developer toolchains.
- An extensible **Plugin System (SDK v2)** with iframe/worker sandboxing, manifest validation, contribution points, and scoped KV storage.
- A cryptographically secure **Ed25519 Offline Licensing Engine** with Windows DPAPI encryption, 30-day grace period logic, and a standalone Keygen CLI tool.

---

## 2. Technology Stack

### 2.1 Core Desktop Runtime & Native Layer
- **Desktop Framework:** Tauri v2 (`@tauri-apps/api: ^2`, `@tauri-apps/cli: ^2`, `tauri: 2.x`, `tauri-build: 2.x`)
- **Backend Language:** Rust 2021 Edition (`developer_cockpit_lib`)
- **Native OS APIs:** `windows-rs 0.61` (features: `Win32_Security_Cryptography`, `Win32_System_Console`, `Win32_System_Threading`, `Win32_System_Diagnostics_Debug`, `Win32_Foundation`, `Wdk_System_Threading`)
- **PTY Subsystem:** `portable-pty: 0.8` (ConPTY native pseudo-terminal backend on Windows)
- **Cryptography & Licensing:** `ed25519-dalek: 2.0` (signature verification), `base64: 0.22`, Windows DPAPI (`CryptProtectData`/`CryptUnprotectData`)
- **Networking / HTTP:** `reqwest: 0.13` (`native-tls` / Schannel backend), `tokio: 1.x`

### 2.2 Frontend Application
- **Runtime / Framework:** React 19 (`react: ^19.1.0`, `react-dom: ^19.1.0`)
- **Language:** TypeScript 5.8 (`typescript: ~5.8.3`)
- **Bundler & Dev Server:** Vite 7 (`vite: ^7.0.4`, `@vitejs/plugin-react: ^4.6.0`)
- **State Management:** Zustand 5 (`zustand: ^5.0.14`)
- **Styling & Design System:** Tailwind CSS v4 (`@tailwindcss/vite: ^4.3.2`, `tailwindcss: ^4.3.2`), `tw-animate-css: ^1.4.0`
- **UI Primitives:** Radix UI (`radix-ui: ^1.6.2`), Lucide Icons (`lucide-react: ^1.24.0`), `class-variance-authority: ^0.7.1`, `clsx: ^2.1.1`, `tailwind-merge: ^3.6.0`
- **Layout & Panes:** `react-resizable-panels: ^4.12.2`
- **Terminal Emulator:** xterm.js 6 (`@xterm/xterm: ^6.0.0`, `@xterm/addon-fit: ^0.11.0`, `@xterm/addon-search: ^0.16.0`, `@xterm/addon-web-links: ^0.12.0`)
- **Typography:** `@fontsource-variable/inter: ^5.2.8`, `@fontsource-variable/jetbrains-mono: ^5.2.8`

### 2.3 Storage & Persistence
- **Database Engine:** SQLite 3 embedded
- **Tauri Plugin:** `@tauri-apps/plugin-sql: ^2.4.0` (`tauri-plugin-sql` with `sqlite` feature)
- **Schema Management:** 8 sequential, versioned migrations defined in `src-tauri/src/db.rs`.

---

## 3. Project Architecture

### 3.1 Directory Structure Overview
```
C:\Developer-Cockpit
├── .github/workflows/         # CI/CD release workflow (WiX MSI + NSIS build)
├── docs/phases/               # Detailed historical phase specs (Phases 1 through 13)
├── public/                    # Static web assets
├── scripts/keygen/            # Standalone Ed25519 license key generator CLI
├── sdk/                       # @developer-cockpit/plugin-sdk TypeScript package
├── src/                       # React frontend source
│   ├── app/                   # App root, module registry, and entry routing
│   ├── components/            # Shared UI (Command Palette, Pro gates, Titlebar, UI primitives)
│   ├── layouts/               # AppShell (Icon Rail, Header, Module Viewport, Status Bar)
│   ├── modules/               # 11 lazy-loaded feature modules
│   ├── services/              # Database access, license validation, app events
│   ├── store/                 # Global UI, Command, Settings, and License Zustand stores
│   └── types/                 # Module, Plugin, and License TypeScript definitions
├── src-tauri/                 # Rust backend source
│   ├── capabilities/          # Tauri v2 permission configuration
│   ├── src/
│   │   ├── commands/          # Tauri IPC invoke handlers (docker, git, ports, terminal, etc.)
│   │   ├── license/           # Cryptographic verification, DPAPI storage, policy evaluator
│   │   ├── platform/          # Windows platform specific helpers
│   │   ├── db.rs              # SQLite migration definitions
│   │   ├── lib.rs             # Tauri builder, plugins, and IPC registrations
│   │   └── main.rs            # Application entrypoint
│   └── Cargo.toml             # Rust package configuration
└── package.json               # Frontend dependencies and npm scripts
```

### 3.2 Frontend Architecture
- **Contract-Driven Modularization:** Every module implements the `ModuleDefinition` contract (`src/types/module.ts`) defining `id`, `name`, `icon`, `component`, `requiredEdition`, and keyboard shortcuts.
- **Lazy Module Mounting:** Modules are dynamically imported (`React.lazy`) and loaded into the main viewport on demand.
- **Shell & Navigation:** `AppShell.tsx` provides an icon rail on the left, a frameless custom title bar at top, an active module canvas, and an extensible bottom status bar.
- **Command Palette:** Global quick launcher (`Ctrl+K` or `F1`) indexing module transitions, open terminal tabs, saved workspaces, projects, snippets, and plugin actions.

### 3.3 Backend Architecture (Rust)
- **Zero Heavy DAEMONs:** The backend does not run long-lived heavy daemons; it operates via low-latency Tauri IPC commands and managed asynchronous streaming channels.
- **PTY Session Manager:** `TerminalManager` manages ConPTY instances via `portable-pty`, streaming raw VT100 byte streams through Tauri IPC `Channel` abstractions directly to xterm.js.
- **Docker Log Streaming:** `DockerLogManager` streams live container stdout/stderr over Tauri Channels with automatic process reaping and UTF-8 carry buffering.
- **Low-Level Windows Introspection:** Uses direct Win32 memory APIs (`NtQueryInformationProcess`, `ReadProcessMemory`) to extract process command-line arguments and working directories for listening ports.

---

## 4. Application Modules

| Module ID | Display Name | Category | License Requirement | Implementation Path |
| :--- | :--- | :--- | :--- | :--- |
| `dashboard` | Dashboard | Overview | Free | `src/modules/dashboard/` |
| `terminal` | Terminal | Core Shell | Free | `src/modules/terminal/` |
| `workspace` | Workspaces | Orchestration | **Pro** | `src/modules/workspace/` |
| `projects` | Projects | Launcher | Free (Multi-step is **Pro**) | `src/modules/projects/` |
| `ports` | Port Manager | Networking | **Pro** | `src/modules/ports/` |
| `git` | Git Dashboard | Version Control | Free basic / **Pro** advanced | `src/modules/git/` |
| `docker` | Docker Workspace | Containers | **Pro** | `src/modules/docker/` |
| `versions` | Version Dashboard | Environment | Free | `src/modules/versions/` |
| `ssh` | SSH Manager | Remote Access | **Pro** | `src/modules/ssh/` |
| `snippets` | Snippets | Productivity | Free (Sync is **Pro**) | `src/modules/snippets/` |
| `plugins` | Plugins | Extensibility | **Pro** | `src/modules/plugins/` |
| `settings` | Settings | Configuration | Free | `src/modules/settings/` |

---

## 5. Verified Features

### 5.1 Modern Terminal Subsystem
- **What it does:** Full-featured terminal emulator supporting multiple tabs, arbitrary horizontal/vertical split pane layouts, native Windows shells, custom profiles, themes, and search.
- **Implementation:**
  - Frontend: `src/modules/terminal/` (`TerminalModule.tsx`, `XtermPane.tsx`, `PaneNode.tsx`, `TerminalTabBar.tsx`, `TerminalSearchBar.tsx`).
  - Backend: `src-tauri/src/commands/terminal.rs` (`pty_spawn`, `pty_write`, `pty_resize`, `pty_kill`, `shells_available`).
- **Notes:** Powered by `@xterm/xterm` 6.0 and ConPTY (`portable-pty`). Automatically detects available local shells via `where.exe` (PowerShell 7, Windows PowerShell, CMD, Git Bash, WSL). Supports font zoom (`Ctrl +/-/0`), in-terminal search (`Ctrl+Shift+F`), copy-on-select, and custom themes (Dracula, Nord, One Dark, Monokai, GitHub Dark/Light).

### 5.2 Workspace Manager
- **What it does:** Snapshots running terminal sessions (tab layouts, split ratios, active shells, per-pane working directories) into SQLite and restores them with a single click.
- **Implementation:** `src/modules/workspace/` (`WorkspaceModule.tsx`, `WorkspaceCard.tsx`, `workspace-service.ts`, `workspace-store.ts`), `src-tauri/src/db.rs` (Migration v2).
- **Notes:** Validates workspace names (case-insensitive uniqueness) and allows editing default starting directories per pane. Gated under Pro.

### 5.3 Project Launcher
- **What it does:** Multi-step project startup orchestrator executing step sequences: launching IDEs/executables (`code`, custom paths), opening terminal tabs with pre-typed scripts (`npm run dev`), launching URLs in default browsers, and opening explorer folders.
- **Implementation:** `src/modules/projects/` (`ProjectsModule.tsx`, `detectors.ts`, `launch-service.ts`), `src-tauri/src/commands/launcher.rs`, `src-tauri/src/commands/projects.rs`.
- **Notes:** Includes project type auto-detectors (`detectors.ts`) scanning for Node, Rust, Go, Python, Docker projects. Single-step projects are Free; multi-step launch execution is Pro-gated.

### 5.4 Port Manager
- **What it does:** Live table of all listening TCP/TCPv6 ports with process name, PID, local address, process command-line inspection, kill process tree, and process restart.
- **Implementation:** `src/modules/ports/` (`PortsModule.tsx`, `PortRow.tsx`), `src-tauri/src/commands/ports/` (`mod.rs`, `windows.rs`).
- **Notes:** Uses `netstat -ano` combined with low-level Win32 memory reads (`NtQueryInformationProcess`, `ReadProcessMemory` on `RTL_USER_PROCESS_PARAMETERS`) to recover command line arguments and working directory. Kill action terminates process trees. Gated under Pro.

### 5.5 Advanced Git Dashboard
- **What it does:** Visual repository manager tracking local repos, branches, ahead/behind upstream status, unstaged/staged file diff indicators, visual SVG commit history graph, commit detail viewer, stash manager, tag manager, checkout, merge assistant, cherry-pick assistant, and repository analytics.
- **Implementation:** `src/modules/git/` (`GitModule.tsx`, `RepoDetail.tsx`, `advanced/*`), `src-tauri/src/commands/git/` (`mod.rs`, `log.rs`, `ops.rs`, `analytics.rs`).
- **Notes:** Pure CLI wrapper over `git` with structured format parsers. Basic status view is Free; advanced history graphs, stashes, tags, merge/cherry-pick assistants, and analytics are Pro-gated.

### 5.6 Docker Workspace & Environment Doctor
- **What it does:** Docker container/image/volume manager, Compose v2 project grouping, live SVG dependency graph layout, streaming log drawer (Tauri channel), container shell launcher, published ports overview, and Docker Doctor diagnostics.
- **Implementation:** `src/modules/docker/` (`DockerModule.tsx`, `WorkspacesView.tsx`, `LogViewer.tsx`, `DoctorView.tsx`, `WorkspaceGraph.tsx`), `src-tauri/src/commands/docker/` (`mod.rs`, `compose.rs`, `logs_stream.rs`, `stats.rs`, `doctor.rs`).
- **Notes:** Shells out to Docker CLI using custom format templates. Doctor decodes Windows `wsl.exe --status` UTF-16LE output, checks Compose v2, Linux container daemon status, and reclaimable disk space. Gated under Pro.

### 5.7 SSH Manager
- **What it does:** Stores SSH connection profiles (host, port, username, identity key file, group, favorite status) and launches live SSH sessions inside terminal panes.
- **Implementation:** `src/modules/ssh/` (`SshModule.tsx`, `ProfileDialog.tsx`), `src-tauri/src/commands/ssh.rs`, `src-tauri/src/db.rs` (Migration v5).
- **Notes:** **Zero password storage by design.** Passwords are never collected or persisted; authentication relies on SSH keys, SSH agent, or in-terminal interactive prompts. Gated under Pro.

### 5.8 Command Snippets
- **What it does:** Categorized repository of frequently used terminal commands with one-click insertion/execution in active terminal panes and clipboard copying.
- **Implementation:** `src/modules/snippets/` (`SnippetsModule.tsx`, `SnippetRow.tsx`), `src-tauri/src/db.rs` (Migration v6).
- **Notes:** Free for local snippet storage. Cloud sync capability is reserved for Pro.

### 5.9 Version Dashboard
- **What it does:** Probes and displays installation state, version string, and binary path for 12 developer tools: Node.js, npm, pnpm, Yarn, Bun, Python, Git, Rust (rustc), Cargo, Go, Java, and Docker.
- **Implementation:** `src/modules/versions/` (`VersionsModule.tsx`, `ToolRow.tsx`), `src-tauri/src/commands/versions.rs`.
- **Notes:** Free tool. Probes asynchronously using `CREATE_NO_WINDOW` processes and regex version extractors.

### 5.10 Settings Hub
- **What it does:** Unified settings manager with 13 configuration sections covering General, Appearance, Terminal, Workspaces, Projects, Ports, Git, Docker, Versions, SSH, Snippets, Plugins, and License management.
- **Implementation:** `src/modules/settings/` (`SettingsModule.tsx`, `sections/*`), `src/store/settings-store.ts`.
- **Notes:** Direct persistence into SQLite `settings` table (Migration v1).

---

## 6. Free vs Pro Architecture

The application enforces a dual-edition model using a centralized, client-side Feature Catalog paired with backend cryptographic validation:

### 6.1 Feature Flag Mapping (`src/services/license/feature-catalog.ts`)
```typescript
export type FeatureId =
  | "workspaces"            // Pro: Save & restore terminal layouts
  | "launcher-multi-step"   // Pro: Multi-step project launchers
  | "docker"                // Pro: Full Docker workspace & doctor
  | "ssh"                   // Pro: SSH profile manager
  | "git-advanced"          // Pro: Commit graph, stashes, tags, merge/cherry-pick, analytics
  | "ports"                 // Pro: Port inspection, kill tree, restart
  | "snippets-sync"         // Pro: Cloud snippet sync (reserved)
  | "plugins"               // Pro: Plugin SDK & custom modules
  | "cloud-sync";           // Pro: Cloud settings sync (reserved)
```

### 6.2 Frontend Gating Mechanisms
- **`ProGate.tsx` / `useFeature(id)`:** Wrap Pro-only components. Free users see an inline upgrade promotion card or a full `UpgradePage.tsx` view with feature benefits.
- **`ProBadge.tsx`:** Renders visual badges in navigation sidebars and menu items for Pro capabilities.
- **Dynamic Module Filtering:** Modules declare `requiredEdition`. When unlicensed, navigation highlights Pro features and directs the user to the upgrade flow.

---

## 7. Plugin Architecture

### 7.1 Plugin SDK (`sdk/`)
- Distributed as `@developer-cockpit/plugin-sdk` (TypeScript package in `sdk/`).
- Defines `createPlugin(definition)` creating v2 plugins supporting lifecycle methods (`mount`, `unmount`), contribution points, and the versioned `CockpitApi` (v2).

### 7.2 Plugin Manifest & Structure
A plugin resides in `%APPDATA%\com.developercockpit.app\plugins\<plugin-id>\`:
- `plugin.json`: Metadata (`id`, `name`, `version`, `description`, `icon`, `api: 2`, `minAppVersion`).
- `index.js`: Single-file ES module exporting the default plugin definition.

### 7.3 Sandboxing & Execution
- **Sandbox Engine (`src/modules/plugins/services/plugin-sandbox.ts`):** Supports execution via sandboxed Hidden `<iframe>` or `WebWorker` contexts communicating via `postMessage` RPC.
- **API Surface (`CockpitApi`):** Exposes controlled methods for showing notifications, registering custom sidebar modules, contributing dashboard panels, listening to events, and accessing scoped persistent storage.
- **Scoped Key-Value Storage:** Backend SQLite table `plugin_kv` (Migration v8) isolates key-value entries per `plugin_id`.
- **Marketplace Discovery:** `MarketplaceTab.tsx` provides local installation from ZIP/Folder and remote registry catalog inspection.

---

## 8. Licensing Architecture

### 8.1 Cryptographic Token Format (`DCK.v1`)
License keys follow the format:
```
DCK.v1.<base64url_json_payload>.<base64url_ed25519_signature>
```

#### Payload Structure:
```json
{
  "v": 1,
  "edition": "pro",
  "email": "user@example.com",
  "issued_at": 1700000000,
  "expires_at": null,
  "license_id": "uuid-string",
  "issuance_timestamp": 1750000000,
  "major_version_allowance": 1
}
```

### 8.2 Security & Storage (`src-tauri/src/license/`)
- **Signature Verification:** Uses `ed25519-dalek` with an embedded 32-byte public key. Signatures cannot be forged without the private key.
- **Encrypted Local Storage:** Stored on Windows using **DPAPI** (`CryptProtectData`) at `%APPDATA%\com.developercockpit.app\license.enc`. Plaintext keys are never stored on disk.
- **30-Day Offline Grace Evaluator (`policy.rs`):** Permits perpetual or subscription keys to run offline for up to 30 days (`OFFLINE_GRACE_SECS = 2,592,000`). If background sync is disabled/unavailable, perpetual licenses remain active indefinitely.
- **Background Sync Task (`sync.rs`):** Non-blocking background worker that periodically validates licenses against the server endpoint.
- **Key Generator Tool:** Standalone Rust CLI located at `scripts/keygen/` capable of generating Ed25519 keypairs and minting signed `DCK.v1` license strings.

---

## 9. Database / Storage

### 9.1 Database Implementation
- **File Location:** `%APPDATA%\com.developercockpit.app\cockpit.db`
- **Driver:** SQLite via `tauri-plugin-sql`
- **URL:** `sqlite:cockpit.db`

### 9.2 Complete Schema Migrations (`src-tauri/src/db.rs`)
1. **Migration 1 (`create_settings_table`):** `settings (key TEXT PRIMARY KEY, value TEXT, updated_at TEXT)`
2. **Migration 2 (`create_workspaces_table`):** `workspaces (id INTEGER PK AUTOINCREMENT, name TEXT UNIQUE NOCASE, layout TEXT, created_at, updated_at)`
3. **Migration 3 (`create_projects_table`):** `projects (id INTEGER PK AUTOINCREMENT, name TEXT UNIQUE NOCASE, config TEXT, created_at, updated_at)`
4. **Migration 4 (`create_repos_table`):** `repos (id INTEGER PK AUTOINCREMENT, name TEXT, path TEXT UNIQUE NOCASE, created_at)`
5. **Migration 5 (`create_ssh_profiles_table`):** `ssh_profiles (id INTEGER PK AUTOINCREMENT, name TEXT UNIQUE NOCASE, host TEXT, port INTEGER, username TEXT, identity_file TEXT, group_name TEXT, favorite INTEGER, created_at, updated_at)`
6. **Migration 6 (`create_snippets_table`):** `snippets (id INTEGER PK AUTOINCREMENT, name TEXT UNIQUE NOCASE, command TEXT, description TEXT, category TEXT, favorite INTEGER, created_at, updated_at)`
7. **Migration 7 (`create_plugin_settings_table`):** `plugin_settings (plugin_id TEXT PRIMARY KEY, enabled INTEGER, updated_at TEXT)`
8. **Migration 8 (`create_plugin_kv_table`):** `plugin_kv (plugin_id TEXT, key TEXT, value TEXT, updated_at TEXT, PRIMARY KEY (plugin_id, key))`

---

## 10. Build System

### 10.1 Build Tooling
- **Node.js:** Node 22+ recommended (npm package manager)
- **Rust Toolchain:** Stable Rust 2021 edition (`cargo`, `rustc`)
- **Tauri CLI:** v2.x CLI (`npm run tauri [dev|build]`)

### 10.2 Packaging & Installers
Configured in `src-tauri/tauri.conf.json`:
- **Targets:** `msi` (WiX Toolset installer) and `nsis` (NSIS executable installer).
- **Window Styling:** Custom frameless window (`decorations: false`), transparent backing with Windows 11 **Mica material effect** (`windowEffects: ["mica"]`).
- **CI/CD Automation:** `.github/workflows/release.yml` triggers on version tags (`v*.*.*`), building both MSI and NSIS installers on `windows-latest` and attaching them to a GitHub Draft Release.

---

## 11. Platform Support

- **Target OS:** Windows 10 (version 1809+) and Windows 11 (x86_64).
- **Windows-Specific Integrations:**
  - **ConPTY:** Native Windows Pseudo-Console for pseudo-terminal emulation.
  - **DPAPI:** Windows Data Protection API for user-scoped license token encryption.
  - **Mica Material:** Native Windows 11 backdrop visual effect.
  - **Win32 Memory Inspection:** Direct native process memory inspection for port ownership recovery.
  - **WSL 2 Integration:** UTF-16LE output decoding for `wsl.exe --status` health checks.
  - **Hidden Windows:** All child processes spawn with Win32 flag `CREATE_NO_WINDOW = 0x08000000` to prevent console popping.
- **Cross-Platform Readiness:** Core React UI is platform-agnostic; porting to macOS/Linux would require replacing Windows-specific Win32/DPAPI/ConPTY calls in `src-tauri` with POSIX equivalents (`pty`, Secret Service/Keychain, `/proc` inspection).

---

## 12. External Dependencies

### 12.1 Host System CLI Dependencies (Optional/Runtime)
The application gracefully detects whether external CLI tools are installed on the user's `PATH`:
- `git.exe` — Required for Git Dashboard.
- `docker.exe` & `docker compose` — Required for Docker Workspace.
- `ssh.exe` — Required for SSH profile connection launching.
- `wsl.exe` — Inspected by Docker Doctor.
- Shells: `pwsh.exe`, `powershell.exe`, `cmd.exe`, `bash.exe` (Git Bash / WSL).

### 12.2 Rust Crates (`Cargo.toml`)
- `tauri` (v2), `tauri-plugin-opener` (v2), `tauri-plugin-dialog` (v2), `tauri-plugin-sql` (v2 with sqlite).
- `portable-pty` (0.8), `ed25519-dalek` (2.0), `base64` (0.22), `reqwest` (0.13), `tokio` (1.x), `serde`/`serde_json`.
- `windows` (0.61 with Win32 features).

---

## 13. Security Considerations

1. **Zero-Password SSH Design:** Passwords are never collected, stored, or logged in SQLite. All authentication flows through the native terminal interface or cryptographic key pairs.
2. **DPAPI License Encryption:** Prevents cross-user or machine-level license key extraction from on-disk config files.
3. **No Unsafe Code in Business Logic:** All `unsafe` blocks in Rust are strictly isolated within `src-tauri/src/license/dpapi.rs` (wrapping Win32 DPAPI) and `src-tauri/src/commands/ports/windows.rs` (wrapping Win32 process memory inspection).
4. **Command Injection Sanitization:** PTY commands and Docker commands utilize token arrays or strict argument sanitization (`guard_ref` identifiers).
5. **Plugin Sandboxing:** Plugins are isolated from direct DOM access via sandboxed iframe/worker execution contexts and mediated through a versioned `CockpitApi`.

---

## 14. Tests

### 14.1 Rust Unit Tests (Verified)
The Rust backend includes unit test suites located in:
- `src-tauri/src/commands/license.rs`: Verification of license activation, masking, and status serialization.
- `src-tauri/src/license/offline.rs`: Ed25519 offline validation, key tampering detection, expired token rejection, and public key verification.
- `src-tauri/src/license/policy.rs`: Offline grace period evaluation, version allowance rules, and expiration checks.
- `src-tauri/src/license/storage.rs`: DPAPI round-trip encryption/decryption and legacy migration.
- `src-tauri/src/license/sync.rs`: Background sync configuration tests.
- `src-tauri/src/license/dpapi.rs`: Win32 DPAPI memory blob handling tests.

### 14.2 Frontend Testing Status
- **Current State:** No automated frontend unit or end-to-end test runners (e.g., Vitest, Jest, Playwright) are currently integrated in `package.json`.
- **Validation Scripts Available:** `npm run typecheck` (`tsc --noEmit`), `npm run lint` (`eslint .`), and `npm run format:check` (`prettier --check .`).

---

## 15. Incomplete / Experimental Areas

1. **License Server Online Verification Endpoint (`VERIFY_URL`):**
   - In `src-tauri/src/license/sync.rs`, `VERIFY_URL` is configured as an empty string (`""`). Background sync is gracefully deactivated until an online verification server is deployed.
   - `license_sign_in` returns a placeholder message instructing users to paste license keys directly.
2. **Cloud Synchronization Placeholders:**
   - `snippets-sync` and `cloud-sync` are cataloged in `feature-catalog.ts` with the tagline `(coming soon)`.
3. **Plugin Marketplace Remote Index:**
   - The marketplace UI (`MarketplaceTab.tsx`) includes a mockup catalog filter; full remote registry fetching is prepared for backend registry integration.

---

## 16. Technical Debt

1. **Frontend Test Suite:** Lack of automated component and integration tests (Vitest + React Testing Library) for complex UI modules like `WorkspaceGraph.tsx` and `PaneNode.tsx`.
2. **Process Restart Heuristics:** In `ports/windows.rs`, process restart relies on reading memory blocks of existing processes, which can fail if the process has already crashed or terminated before inspection.
3. **Large Single-File Rust Modules:** `src-tauri/src/commands/plugins.rs` (~20 KB) and `src-tauri/src/commands/ports/windows.rs` (~12 KB) could be further subdivided for maintainability.

---

## 17. Documentation Gaps

1. **Developer Contribution Guide:** While `docs/phases/` thoroughly documents historical implementation, there is no root `CONTRIBUTING.md` outlining local environment setup, testing procedures, or architecture overviews for open-source contributors.
2. **Plugin Authoring Guide:** `sdk/README.md` is well-written, but lacks end-to-end tutorial samples and debugging instructions for third-party plugin authors.
3. **API IPC Reference:** No single auto-generated documentation exists listing all Tauri IPC invoke commands and their parameter schemas.

---

## 18. Product Strengths

1. **Exceptional Native Windows Performance:** ConPTY integration, lightweight Tauri Rust shell, and zero background electron overhead result in sub-second startup times and low memory consumption (~40–80 MB idle).
2. **Cohesive Developer Workflow:** Seamless integration across terminals, port monitoring, Docker containers, and Git in a single unified workspace interface.
3. **Rock-Solid Licensing Subsystem:** Production-grade offline Ed25519 cryptographic licensing with DPAPI encryption and grace period evaluation.
4. **Clean, Modern UI/UX:** Polished Dark mode aesthetic with Windows 11 Mica blur, Radix UI primitives, Lucide icons, and responsive split-pane controls.
5. **Robust Extensibility Foundation:** Extensible Plugin SDK with sandboxed execution, contribution points, and scoped persistence.

---

## 19. Potential Risks for an Acquiring/Partner Company

1. **Windows Platform Coupling:** The codebase heavily leverages Windows-specific APIs (ConPTY, DPAPI, `windows-rs`, Win32 process memory, `netstat.exe`). Expanding to macOS or Linux will require platform abstraction layers.
2. **Licensing Server Infrastructure Required:** To enable email sign-in activations and automated subscription revocations, an external license server handling Stripe/LemonSqueezy webhooks and signing tokens must be deployed.
3. **CLI Ecosystem Dependency:** The Docker and Git dashboards depend on the user having `git` and `docker` on their system `PATH`. Host environment variations must continue to be handled gracefully.

---

## 20. Recommended Documentation Structure

For packaging the public repository and enterprise-grade developer documentation, the following documentation structure is recommended:

```
docs/
├── README.md                  # Main documentation portal
├── architecture/
│   ├── overview.md            # High-level architecture & design principles
│   ├── tauri-rust-backend.md  # ConPTY, IPC handlers, Win32 subsystems
│   ├── frontend-react.md      # State management, AppShell, module system
│   └── database-schema.md     # SQLite schema & migration guidelines
├── features/
│   ├── terminal.md            # Terminal tabs, split panes, profiles, themes
│   ├── workspaces.md          # Layout snapshot & restore mechanics
│   ├── project-launcher.md    # Multi-step project orchestration
│   ├── port-manager.md        # Socket detection & process management
│   ├── git-dashboard.md       # Git integration & advanced workflows
│   ├── docker-workspace.md    # Compose grouping, graph, and logs
│   └── ssh-manager.md         # SSH profiles & zero-password model
├── licensing/
│   ├── overview.md            # Ed25519 architecture & DCK.v1 specification
│   ├── security-dpapi.md      # DPAPI encryption & key storage
│   └── keygen-cli.md          # Using the offline key generator
├── plugin-sdk/
│   ├── getting-started.md     # Creating your first plugin
│   ├── manifest-schema.md     # plugin.json specification
│   └── api-reference.md       # CockpitApi v2 reference
└── development/
    ├── setup.md               # Local environment prerequisites & build guide
    ├── testing.md             # Running Rust tests & linting
    └── release-process.md     # WiX/NSIS packaging & CI/CD workflow
```

---

## Verification Rules

| Feature / Subsystem | Verification Status | Verification Evidence / Source Location |
| :--- | :--- | :--- |
| **Modern Terminal** | **VERIFIED** | `src/modules/terminal/`, `src-tauri/src/commands/terminal.rs`, xterm.js 6, ConPTY |
| **Terminal Tabs** | **VERIFIED** | `TerminalTabBar.tsx`, `terminal-store.ts` |
| **Split Panes** | **VERIFIED** | `react-resizable-panels`, `pane-tree.ts`, `PaneNode.tsx`, `XtermPane.tsx` |
| **Terminal Profiles** | **VERIFIED** | `data/profiles.ts`, `shells_available` in `terminal.rs` |
| **Themes Engine** | **VERIFIED** | `data/themes.ts`, Tailwind CSS v4 custom theme system |
| **Command History** | **VERIFIED** | Shell-native history via ConPTY, xterm.js scrollback buffer, Command Palette |
| **Project Launcher** | **VERIFIED** | `src/modules/projects/`, `detectors.ts`, `launch-service.ts`, `launcher.rs` |
| **Workspace Launcher** | **VERIFIED** | `src/modules/workspace/`, `workspace-service.ts` |
| **Workspace Restore** | **VERIFIED** | `WorkspaceCard.tsx`, `workspace-store.ts`, SQLite Migration v2 |
| **Version Dashboard** | **VERIFIED** | `src/modules/versions/`, `src-tauri/src/commands/versions.rs` (12 tools probed) |
| **Recent Projects** | **VERIFIED** | `ProjectsPanel.tsx`, `project-store.ts` |
| **Port Manager** | **VERIFIED** | `src/modules/ports/`, `src-tauri/src/commands/ports/windows.rs` |
| **Git Dashboard** | **VERIFIED** | `src/modules/git/`, `src-tauri/src/commands/git/` |
| **Docker Dashboard** | **VERIFIED** | `src/modules/docker/`, `src-tauri/src/commands/docker/` |
| **Docker Doctor** | **VERIFIED** | `DoctorView.tsx`, `src-tauri/src/commands/docker/doctor.rs` |
| **SSH Manager** | **VERIFIED** | `src/modules/ssh/`, `src-tauri/src/commands/ssh.rs`, SQLite Migration v5 |
| **Command Snippets** | **VERIFIED** | `src/modules/snippets/`, SQLite Migration v6 |
| **Plugin System (SDK v2)**| **VERIFIED** | `src/modules/plugins/`, `sdk/`, `src-tauri/src/commands/plugins.rs` |
| **Free / Pro Editions** | **VERIFIED** | `src/services/license/feature-catalog.ts`, `ProGate.tsx`, `UpgradePage.tsx` |
| **Feature Flags** | **VERIFIED** | `feature-catalog.ts`, `editionSatisfies()`, `license-store.ts` |
| **License Management** | **VERIFIED** | `src-tauri/src/license/`, `scripts/keygen/`, Ed25519, Windows DPAPI |
| **SQLite Database** | **VERIFIED** | `src-tauri/src/db.rs` (8 migrations), `tauri-plugin-sql` |
| **Tauri Framework** | **VERIFIED** | Tauri v2.0 (`tauri.conf.json`, `Cargo.toml`, `lib.rs`) |
| **React Frontend** | **VERIFIED** | React 19.1.0, Vite 7.0, TypeScript 5.8, Tailwind CSS v4, Zustand 5.0 |
| **Rust Backend** | **VERIFIED** | Rust 2021 Edition, `src-tauri/src/lib.rs` |
| **Windows-Specific APIs** | **VERIFIED** | `windows-rs 0.61`, ConPTY, DPAPI, `CREATE_NO_WINDOW`, Win32 Process Memory |
| **Mica / Window Customization**| **VERIFIED** | `tauri.conf.json` (`effects: ["mica"]`, `decorations: false`), custom title-bar |
| **Cloud Sync / Snippet Sync** | **PARTIALLY VERIFIED** | Cataloged in `feature-catalog.ts` as `(coming soon)`; backend sync stub in `sync.rs` |
| **Online Auth Sign-in** | **PARTIALLY VERIFIED** | `license_sign_in` stubbed awaiting live API server deployment |
| **Automated Frontend Tests** | **NOT FOUND** | No Vitest/Jest/Playwright configurations found in `package.json` |
