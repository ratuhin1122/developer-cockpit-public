# Plugin SDK — Complete API Reference

> **Package:** `@developer-cockpit/plugin-sdk`  
> **Source Location:** `sdk/src/index.ts`

---

## 1. `CockpitApi` (v2) Interface

Plugins receive the `CockpitApi` instance during `hooks.activate(api)`, `mount(container, api)`, and command execution:

```typescript
export interface CockpitApi {
  readonly apiVersion: 2;

  /**
   * Open a new tab in the built-in terminal and switch to it.
   */
  openTerminalTab(options?: {
    title?: string;
    cwd?: string;
    command?: string;
  }): void;

  /**
   * Display a non-blocking toast notification in the cockpit.
   */
  notify(options: {
    title: string;
    message?: string;
    kind?: "info" | "success" | "warn" | "error";
  }): void;

  /**
   * Switch the active viewport to a module id (built-in or plugin).
   */
  navigate(moduleId: string): void;

  /**
   * Plugin-scoped persistent key-value storage backed by SQLite.
   * - Keys: `^[a-z0-9._-]{1,64}$`
   * - Values: Strings up to 100 kB
   * - Quota: Up to 50 keys per plugin
   */
  storage: {
    get(key: string): Promise<string | null>;
    set(key: string, value: string): Promise<void>;
    remove(key: string): Promise<void>;
  };

  /**
   * Returns application and plugin API metadata.
   */
  getAppInfo(): Promise<{
    version: string;
    pluginApiVersion: number;
    platform: string;
  }>;
}
```

---

## 2. Contribution Points

A v2 plugin definition provides contributions declaratively to `createPlugin()`:

### 2.1 Sidebar Contribution (`sidebar`)
Contributes a full-page view accessible via the left icon rail:
```typescript
sidebar?: {
  title?: string;
  icon?: IconName;
  mount(container: HTMLElement, api: CockpitApi): () => void;
}
```

### 2.2 Dashboard Panels (`dashboard`)
Contributes cards to the main Overview Dashboard grid:
```typescript
dashboard?: Array<{
  id: string;
  title: string;
  span?: 1 | 2; // 1 = half row (default), 2 = full row
  mount(container: HTMLElement, api: CockpitApi): () => void;
}>;
```

### 2.3 Command Palette Actions (`commands`)
Registers actions into the global `Ctrl+K` palette:
```typescript
commands?: Array<{
  id: string;
  title: string;
  run(api: CockpitApi): void | Promise<void>;
}>;
```

### 2.4 Terminal Command Sugar (`terminalCommands`)
Registers static "open terminal and run command" palette actions without custom JavaScript code:
```typescript
terminalCommands?: Array<{
  id: string;
  title: string;
  command: string;
  cwd?: string;
}>;
```

### 2.5 Context Menus (`contextMenu`)
Appends items to right-click menus on Docker containers or Project cards:
```typescript
contextMenu?: Array<{
  target: "docker-container" | "project-card";
  id: string;
  title: string;
  run(payload: ContextPayload, api: CockpitApi): void | Promise<void>;
}>;
```

### 2.6 Project Import Detectors (`projectDetectors`)
Analyzes directory contents during the Projects module's import scan:
```typescript
projectDetectors?: Array<{
  id: string;
  detect(ctx: {
    path: string;
    fileNames: string[];
  }): DetectedProject | null | Promise<DetectedProject | null>;
}>;
```

---

## 3. Supported Icons (`IconName`)

The following icon identifiers are supported in `plugin.json` and sidebar contributions:
`blocks`, `bot`, `clock`, `database`, `flask`, `gauge`, `globe`, `heart`, `package`, `puzzle` *(default)*, `rocket`, `sparkles`, `star`, `wrench`, `zap`.

---

## 4. Application Event Map (`events`)

Plugins can subscribe to application-wide lifecycle events:

| Event Name | Payload | Trigger |
| :--- | :--- | :--- |
| `module-changed` | `{ moduleId: string }` | Active module switched. |
| `docker-containers-changed` | `{ running: number, total: number }` | Container state update. |
| `project-launched` | `{ name: string }` | Project launch steps executed. |
| `plugins-changed` | `{}` | Plugins enabled, disabled, or reloaded. |
