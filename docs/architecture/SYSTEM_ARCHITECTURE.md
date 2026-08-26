# Developer Cockpit — System Architecture

> **Focus:** End-to-end component breakdown, process model, concurrency, and IPC communication.

---

## 1. Process & Runtime Model

Developer Cockpit executes as a hybrid desktop application utilizing Tauri v2's multi-process architecture on Windows:

```mermaid
graph TB
    subgraph HostProcess ["Core Host Process (developer-cockpit.exe)"]
        direction TB
        TauriCore["Tauri v2 Core Runtime"]
        AsyncRuntime["Tokio Asynchronous Runtime"]
        StateMgr["Managed State Registry\n(TerminalManager, DockerLogManager)"]
        SQLiteEngine["SQLite Embedded Engine (tauri-plugin-sql)"]
    end

    subgraph WebviewProcess ["Webview Process (Microsoft Edge WebView2)"]
        direction TB
        ReactApp["React 19 Frontend (dist/index.html)"]
        XtermInstances["xterm.js Terminal Canvas Instances"]
        PluginFrames["Hidden Sandboxed <iframe> / WebWorkers"]
    end

    subgraph ChildProcesses ["Spawned Child & PTY Processes"]
        direction TB
        ConPTY1["ConPTY Host 1 (pwsh.exe / cmd.exe)"]
        ConPTY2["ConPTY Host 2 (git-bash.exe)"]
        DockerStream["docker logs -f Child Process"]
        OneShotTools["One-Shot CLI Tools (git, docker, netstat, wsl)"]
    end

    HostProcess <==>|WebView2 IPC Bridge| WebviewProcess
    HostProcess -->|portable-pty PTY Pipes| ConPTY1
    HostProcess -->|portable-pty PTY Pipes| ConPTY2
    HostProcess -->|Stdio Piped Reader| DockerStream
    HostProcess -->|CREATE_NO_WINDOW Spawn| OneShotTools
```

### 1.1 Process Boundaries
1. **Core Host Process (`developer-cockpit.exe`):**
   - Implemented in Rust (`developer_cockpit_lib`).
   - Hosts the Tokio async runtime, Tauri event loop, and OS integration hooks.
   - Manages stateful subsystems: PTY master handles, streaming supervisors, and SQLite connection pools.
2. **Renderer Process (Microsoft Edge WebView2):**
   - Runs the compiled React 19 single-page application (`dist/`).
   - Handles DOM manipulation, canvas rendering for xterm.js, CSS animations, and user interactions.
   - Operates in a secure context communicating with the host process via Tauri's JSON-RPC bridge.
3. **Child Subprocesses:**
   - **PTY Processes:** Spawned via `portable-pty` using Windows ConPTY. Each terminal pane owns a dedicated PTY subprocess.
   - **Streaming Daemons:** Child processes (such as `docker logs -f`) spawned with piped stdout/stderr.
   - **One-Shot Probes:** Short-lived CLI commands (`git status`, `netstat -ano`, `wsl.exe --status`, `where.exe`) executed with Win32 flag `CREATE_NO_WINDOW = 0x08000000` to prevent console window flickering.

---

## 2. Inter-Process Communication (IPC) Patterns

Developer Cockpit utilizes three distinct IPC patterns across its lifecycle:

```mermaid
sequenceDiagram
    participant Webview as React Webview
    participant TauriBridge as Tauri IPC Bridge
    participant RustBackend as Rust Command Handler
    participant OS as OS / CLI Subprocess

    %% 1. Request-Response
    Note over Webview, RustBackend: Pattern 1: Asynchronous Request-Response (Invoke)
    Webview->>TauriBridge: invoke('list_ports')
    TauriBridge->>RustBackend: commands::ports::list_ports()
    RustBackend->>OS: Execute netstat & inspect Win32 memory
    OS-->>RustBackend: Socket data
    RustBackend-->>TauriBridge: Return Vec<PortEntry> (JSON)
    TauriBridge-->>Webview: Resolve Promise<PortEntry[]>

    %% 2. High-Throughput Streaming
    Note over Webview, OS: Pattern 2: Bidirectional Channel Streaming (PTY / Logs)
    Webview->>TauriBridge: invoke('pty_spawn', { channel, shell, cwd })
    RustBackend->>OS: ConPTY spawn (portable-pty)
    RustBackend-->>Webview: Return sessionId
    loop PTY Output Streaming
        OS->>RustBackend: Raw bytes on PTY read thread
        RustBackend->>TauriBridge: Channel.send(PtyEvent::Output { data })
        TauriBridge->>Webview: OnChannelMessage -> xterm.write(data)
    end
    Webview->>TauriBridge: invoke('pty_write', { id, data: "ls\r" })
    RustBackend->>OS: PTY master.write_all("ls\r")

    %% 3. Global Broadcast Events
    Note over Webview, RustBackend: Pattern 3: Global Broadcast Events
    RustBackend->>TauriBridge: app.emit('license:refreshed')
    TauriBridge->>Webview: window.listen('license:refreshed')
```

---

## 3. Concurrency & Threading Model

To ensure the UI thread never freezes, heavy operations and long-running streams execute across dedicated asynchronous and worker threads:

```mermaid
graph LR
    subgraph HostThreads ["Rust Threading Model"]
        MainThread["Main / GUI Event Loop"]
        TokioPool["Tokio Asynchronous Worker Pool (Multi-threaded)"]
        
        subgraph PTYThreads ["PTY Dedicated OS Threads (Per Pane)"]
            PTYReader["PTY Reader Thread (Blocking Read)"]
            PTYCarry["UTF-8 Carry Buffer State"]
        end

        subgraph DockerThreads ["Docker Log Streaming Threads"]
            StdoutReader["Stdout Reader Thread"]
            StderrReader["Stderr Reader Thread"]
            StreamSupervisor["Supervisor / Reaper Thread"]
        end
    end

    MainThread -->|Spawns| TokioPool
    TokioPool -->|Spawns| PTYThreads
    TokioPool -->|Spawns| DockerThreads
```

### 3.1 PTY Stream Isolation
- Every active terminal pane allocates a background OS thread dedicated to blocking reads on the PTY stdout descriptor.
- Output bytes pass through a custom UTF-8 carry-buffer to prevent multi-byte characters (such as emojis or non-ASCII identifiers) from being split across streaming chunk boundaries before transmission to the webview.

### 3.2 Docker Stream Supervision
- Spawns independent stdout and stderr reader threads.
- A supervisor thread monitors child process exit status, cleans up file descriptors, prevents zombie processes, and dispatches an `Exit` event over the Tauri Channel.

---

## 4. Application Lifecycle & Boot Sequence

```mermaid
sequenceDiagram
    autonumber
    participant Main as Tauri main() / lib.rs
    participant DB as SQLite Migration Engine
    participant Lic as License Background Sync
    participant Webview as WebView2 Window
    participant React as React App (main.tsx)

    Main->>DB: Apply schema migrations (v1 through v8)
    Main->>Lic: Spawn non-blocking background license task (sync::run)
    Main->>Webview: Create frameless window (Mica material, Dark theme)
    Webview->>React: Mount main.tsx
    React->>React: Initialize Zustand stores (UI, Settings, License)
    React->>Main: invoke('license_status')
    Main-->>React: Return LicenseStatus (Edition, State, GraceDays)
    React->>React: Mount AppShell & Default Module (Terminal / Last Used)
    Note over Lic, Main: 10s after boot: License background sync verifies token
```

---

## 5. Subsystem Summary

| Subsystem | Primary Implementation | Threading Strategy | Failure Isolation |
| :--- | :--- | :--- | :--- |
| **Terminal PTY** | `src-tauri/src/commands/terminal.rs` | 1 dedicated OS thread per pane | PTY crash emits `Exit` event without crashing the host app. |
| **Port Inspection** | `src-tauri/src/commands/ports/` | Tokio worker task | Win32 memory read failures fall back gracefully to basic netstat info. |
| **Docker Engine** | `src-tauri/src/commands/docker/` | Tokio worker + dedicated log readers | Missing Docker CLI or daemon offline returns structured diagnostic errors. |
| **Git Engine** | `src-tauri/src/commands/git/` | Tokio worker task | Corrupt repositories or missing git binaries return graceful error states. |
| **Database** | `src-tauri/src/db.rs` | Connection pool via `tauri-plugin-sql` | Migrations run sequentially on boot within a single transaction. |
| **Licensing** | `src-tauri/src/license/` | Tokio non-blocking timer | Background verification network failures are completely silent. |
