# Authoring & Developing Plugins

> **Guide:** Building, bundling, and testing Developer Cockpit plugins with TypeScript or JavaScript.

---

## 1. Quick Start (Vanilla JavaScript)

No build tools are required for vanilla JavaScript:

1. Open the Plugins module and click **"Open folder"** to reveal `%APPDATA%\com.developercockpit.app\plugins\`.
2. Create a folder `hello-plugin`.
3. Create `plugin.json`:
   ```json
   {
     "id": "hello-plugin",
     "name": "Hello Plugin",
     "version": "1.0.0",
     "icon": "sparkles",
     "api": 2
   }
   ```
4. Create `index.js`:
   ```javascript
   export default {
     id: "hello-plugin",
     name: "Hello Plugin",
     version: "1.0.0",
     sidebar: {
       title: "Hello Plugin",
       mount(container, api) {
         container.innerHTML = `<div style="padding: 24px; color: #fff;">
           <h1>Hello from Developer Cockpit Plugin!</h1>
           <button id="btn" style="padding: 8px 16px; background: #3b82f6; color: white; border: none; border-radius: 4px; cursor: pointer;">
             Open Terminal
           </button>
         </div>`;

         const btn = container.querySelector("#btn");
         btn.onclick = () => api.openTerminalTab({ command: "echo Hello Plugin!" });

         return () => {
           btn.onclick = null;
         };
       }
     }
   };
   ```
5. In Developer Cockpit, click **"Reload"** and toggle the switch to enable your plugin!

---

## 2. Professional TypeScript Development Workflow

For larger plugins, authoring with TypeScript and bundling via `esbuild` or `vite` is recommended:

### 2.1 Project Initialization
```bash
mkdir my-cockpit-plugin
cd my-cockpit-plugin
npm init -y
npm install --save-dev typescript esbuild @developer-cockpit/plugin-sdk
```

### 2.2 Writing the Plugin (`src/index.ts`)
```typescript
import { createPlugin, type CockpitApi } from "@developer-cockpit/plugin-sdk";

export default createPlugin({
  id: "my-cockpit-plugin",
  name: "My Cockpit Plugin",
  version: "1.0.0",
  sidebar: {
    title: "My Plugin",
    icon: "rocket",
    mount(container: HTMLElement, api: CockpitApi) {
      const heading = document.createElement("h2");
      heading.textContent = "Custom Workspace View";
      container.appendChild(heading);

      return () => {
        container.innerHTML = "";
      };
    },
  },
  commands: [
    {
      id: "quick-ping",
      title: "Ping Gateway",
      run(api) {
        api.notify({
          title: "Gateway Ping",
          message: "Local gateway online.",
          kind: "success",
        });
      },
    },
  ],
});
```

### 2.3 Bundling to `dist/index.js`
Plugins must export a single-file ES module. Build using `esbuild`:

```bash
npx esbuild src/index.ts --bundle --format=esm --outfile=dist/index.js
```

Copy `plugin.json` and `dist/index.js` into your `%APPDATA%\com.developercockpit.app\plugins\my-cockpit-plugin\` folder.
