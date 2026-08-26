# Developer Cockpit — Architecture Documentation

This directory contains in-depth, verified technical architecture documentation for Developer Cockpit.

---

## Architecture Documents

1. **[Architectural Overview](./OVERVIEW.md)**  
   *High-level system vision, end-to-end execution flow diagram, and architectural layers.*

2. **[System Architecture](./SYSTEM_ARCHITECTURE.md)**  
   *Process model, thread concurrency, PTY stream isolation, and Tauri IPC patterns.*

3. **[Frontend Architecture](./FRONTEND_ARCHITECTURE.md)**  
   *React 19 structure, Zustand state management, module contract, xterm.js integration, and UI optimizations.*

4. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)**  
   *Rust 2021 crate layout, command handlers, ConPTY manager, and Docker/Git/Port execution engines.*

5. **[Data Architecture](./DATA_ARCHITECTURE.md)**  
   *SQLite schema migrations (v1 through v8), entity-relationship diagram, and persistence contracts.*

6. **[Feature Flag Architecture](./FEATURE_FLAG_ARCHITECTURE.md)**  
   *Declarative feature catalog, capability gating, and dynamic flag resolution.*

7. **[Edition Architecture](./EDITION_ARCHITECTURE.md)**  
   *Free vs. Pro boundaries, comprehensive feature matrix, and UI gating patterns.*

8. **[Licensing Architecture](./LICENSING_ARCHITECTURE.md)**  
   *Ed25519 digital signature validation, DCK.v1 token format, DPAPI storage, and 30-day offline policy.*

9. **[Plugin Architecture](./PLUGIN_ARCHITECTURE.md)**  
   *Plugin SDK v2, sandboxed execution runtime, manifest schema, and contribution points.*

10. **[Security Architecture](./SECURITY_ARCHITECTURE.md)**  
    *Threat modeling, zero-password SSH policy, memory safety boundaries, and sandbox isolation.*

11. **[Windows Platform Integration](./WINDOWS_INTEGRATION.md)**  
    *ConPTY pseudo-consoles, Win32 process memory introspection, DPAPI, Mica blur, and WSL2 support.*
