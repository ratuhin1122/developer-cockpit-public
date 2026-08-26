# Terminal Settings & Themes Engine

> **Status:** VERIFIED (Phase 2 & Phase 12 Implementation)  
> **Source Locations:** `src/modules/terminal/data/themes.ts`, `src/modules/terminal/components/TerminalSettingsForm.tsx`, `src/modules/settings/sections/terminal.tsx`

---

## Overview

The **Terminal Settings & Themes Engine** provides fine-grained visual, operational, and behavioral customization for all terminal instances across the application, applying changes live without requiring terminal restarts.

---

## Problem It Solves

Standard terminals often require static configuration files (JSON or YAML) that require restarting shell sessions to preview changes. Developer Cockpit exposes live-updating visual settings and color themes that reconfigure active xterm.js instances immediately.

---

## Capabilities

- **Curated Color Themes:** Built-in color schemes matching modern developer palettes:
  - **Dracula** (Dark)
  - **Nord** (Dark)
  - **One Dark** (Dark)
  - **Monokai Pro** (Dark)
  - **GitHub Dark** (Dark)
  - **GitHub Light** (Light)
- **Typography & Font Sizing:** Custom monospace font selection (`JetBrains Mono`, `Fira Code`, `Consolas`, `Cascadia Code`, `Source Code Pro`), font size adjustment (8px–32px), and line height tuning (1.0–2.0).
- **Cursor Customization:** Configurable cursor shapes (`block`, `underline`, `bar`) and cursor blinking toggles.
- **Scrollback Tuning:** Adjustable buffer depth from 1,000 to 50,000 lines.
- **Clipboard Ergonomics:** Optional copy-on-select toggle and right-click paste behavior.

---

## Technical Implementation

- **Theme Definitions (`src/modules/terminal/data/themes.ts`):** Implements exact xterm `ITheme` 16-color ANSI definitions (black, red, green, yellow, blue, magenta, cyan, white, bright variants, background, foreground, selection, and cursor colors).
- **Live Reconfiguration:** `terminal-instances.ts` subscribes to `terminal-settings-store.ts`. When a setting or theme changes, `term.options` is updated immediately across all mounted panes without dropping the active PTY session.

---

## Free / Pro Availability

- **Free Edition:** 100% available with all themes and settings options.
- **Pro Edition:** 100% available.

---

## Configuration

Persisted in the SQLite database under the `settings` table key `terminal_settings`:

```json
{
  "theme": "dracula",
  "fontFamily": "JetBrains Mono Variable",
  "fontSize": 13,
  "lineHeight": 1.2,
  "cursorStyle": "block",
  "cursorBlink": true,
  "scrollback": 5000,
  "copyOnSelect": false
}
```

---

## Limitations

- Custom user-imported `.itermcolors` or VS Code JSON themes are not currently parsed via UI upload; users select from the curated palette list.

---

## Future Improvements

- [ ] Support for importing standard TextMate / VS Code `.json` color themes.
- [ ] Per-profile custom color schemes (e.g., distinctive red backdrop for root/production SSH sessions).
