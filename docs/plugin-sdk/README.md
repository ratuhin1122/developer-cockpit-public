# Developer Cockpit Plugin SDK Documentation

This directory contains technical guides, API references, sandboxing specifications, and code examples for the Developer Cockpit Plugin SDK (`@developer-cockpit/plugin-sdk`).

---

## Documentation Index

1. **[Plugin SDK Overview](./OVERVIEW.md)**  
   *Extensibility vision, core design principles, and runtime architecture.*

2. **[Runtime Architecture](./ARCHITECTURE.md)**  
   *Host discovery, sandboxing boundaries, postMessage RPC bridge, and SQLite persistence.*

3. **[Plugin Lifecycle & State Machine](./PLUGIN_LIFECYCLE.md)**  
   *Discovery, validation, trust decisions, activation, view mounting, and hot reloading.*

4. **[Complete API Reference](./API.md)**  
   *Detailed documentation for `CockpitApi` v2 and all contribution point schemas.*

5. **[Authoring & Developing Plugins](./DEVELOPING_PLUGINS.md)**  
   *Step-by-step development guide, TypeScript setup, and bundling with esbuild.*

6. **[Plugin Distribution & Installation](./INSTALLATION.md)**  
   *Installation via ZIP archives, folder selection, and manual drop-in.*

7. **[Security Architecture & Sandboxing](./SECURITY.md)**  
   *DOM isolation, storage quotas, namespace boundaries, and permission models.*

8. **[Verified Code Examples](./EXAMPLES.md)**  
   *Working examples: Custom sidebar tools, dashboard deploy counters, and project detectors.*

9. **[Extensibility Roadmap](./ROADMAP.md)**  
   *Current verified capabilities vs. planned marketplace and permission enhancements.*
