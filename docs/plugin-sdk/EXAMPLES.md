# Plugin SDK — Verified Examples

> **Status:** VERIFIED against `@developer-cockpit/plugin-sdk` (v2)  
> **Source Location:** `sdk/README.md` and `src-tauri/src/commands/plugins.rs`

---

## 1. Example 1: Sidebar Module with Terminal Action

Creates a custom sidebar tool with interactive buttons triggering terminal commands and toast notifications:

### `plugin.json`
```json
{
  "id": "hello-sdk",
  "name": "Hello SDK",
  "version": "1.0.0",
  "description": "Demonstrates SDK v2 contributions and terminal actions.",
  "icon": "rocket",
  "api": 2,
  "minAppVersion": "0.1.0"
}
```

### `index.js`
```javascript
import { createPlugin } from "@developer-cockpit/plugin-sdk";

export default createPlugin({
  id: "hello-sdk",
  name: "Hello SDK",
  version: "1.0.0",
  sidebar: {
    title: "Hello SDK",
    icon: "rocket",
    mount(container, api) {
      container.innerHTML = `
        <div style="padding: 24px; color: #fff; font-family: sans-serif;">
          <h2 style="margin-bottom: 12px;">Hello from Plugin SDK v2!</h2>
          <p style="color: #94a3b8; margin-bottom: 20px;">
            This custom sidebar module is rendered from a sandboxed plugin.
          </p>
          <div style="display: flex; gap: 12px;">
            <button id="btn-term" style="padding: 8px 16px; background: #3b82f6; color: white; border: none; border-radius: 6px; cursor: pointer;">
              Open Terminal
            </button>
            <button id="btn-toast" style="padding: 8px 16px; background: #334155; color: white; border: none; border-radius: 6px; cursor: pointer;">
              Show Notification
            </button>
          </div>
        </div>
      `;

      const btnTerm = container.querySelector("#btn-term");
      const btnToast = container.querySelector("#btn-toast");

      btnTerm.onclick = () => {
        api.openTerminalTab({
          title: "Plugin Shell",
          command: "echo 'Launched from Hello SDK Plugin!'",
        });
      };

      btnToast.onclick = () => {
        api.notify({
          title: "Plugin Notification",
          message: "Greetings from the SDK sandbox!",
          kind: "success",
        });
      };

      return () => {
        btnTerm.onclick = null;
        btnToast.onclick = null;
        container.innerHTML = "";
      };
    },
  },
});
```

---

## 2. Example 2: Overview Dashboard Card with Persistent Storage

Contributes a real-time metric widget to the Overview Dashboard using `api.storage`:

```javascript
import { createPlugin } from "@developer-cockpit/plugin-sdk";

export default createPlugin({
  id: "counter-panel",
  name: "Counter Panel",
  version: "1.0.0",
  dashboard: [
    {
      id: "click-counter",
      title: "Daily Deploy Counter",
      span: 1,
      mount(container, api) {
        let count = 0;

        container.innerHTML = `
          <div style="padding: 16px; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%;">
            <div id="count-display" style="font-size: 32px; font-weight: bold; margin-bottom: 8px;">0</div>
            <button id="inc-btn" style="padding: 6px 12px; background: #22c55e; color: white; border: none; border-radius: 4px; cursor: pointer;">
              Increment Deploy
            </button>
          </div>
        `;

        const display = container.querySelector("#count-display");
        const btn = container.querySelector("#inc-btn");

        // Load saved state
        api.storage.get("deploy_count").then((val) => {
          if (val) {
            count = parseInt(val, 10);
            display.textContent = count;
          }
        });

        btn.onclick = async () => {
          count++;
          display.textContent = count;
          await api.storage.set("deploy_count", count.toString());
        };

        return () => {
          btn.onclick = null;
        };
      },
    },
  ],
});
```

---

## 3. Example 3: Custom Project Detector

Scans imported folders during project imports and proposes custom launch steps when specific proprietary files are present:

```javascript
import { createPlugin } from "@developer-cockpit/plugin-sdk";

export default createPlugin({
  id: "deno-detector",
  name: "Deno Project Detector",
  version: "1.0.0",
  projectDetectors: [
    {
      id: "deno-json-detector",
      detect(ctx) {
        if (ctx.fileNames.includes("deno.json") || ctx.fileNames.includes("deno.jsonc")) {
          const folderName = ctx.path.split(/[\\/]/).pop() || "Deno App";
          return {
            name: folderName,
            terminalCommand: "deno task dev",
            url: "http://localhost:8000",
          };
        }
        return null;
      },
    },
  ],
});
```
