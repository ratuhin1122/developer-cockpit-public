# Free Edition Architecture & Scope

> **Status:** Verified against Developer Cockpit v0.1.0 codebase.

---

## 1. Free Edition Principles

The **Free Edition** of Developer Cockpit is designed as a standalone, zero-friction developer workstation tool. It adheres to three strict product standards:
1. **Zero Registration / Zero Sign-in:** The app requires no account creation, no email submission, and no internet connectivity to run.
2. **No Artificial Friction in Core Tools:** The core Terminal subsystem has zero limits on tabs, split panes, shell choices, custom themes, or buffer depth.
3. **Transparent Upgrade Discovery:** Pro features are indicated with discreet badges (`PRO`) and inline benefit summaries; there are no intrusive popups or timer-based nagging modals.

---

## 2. Complete Free Edition Capabilities

| Subsystem | Included Free Capabilities | Gated Pro Capabilities (for context) |
| :--- | :--- | :--- |
| **Modern Terminal** | :white_check_mark: Unlimited tabs, split panes, all 6 themes, dynamic font zoom, search, all local shells (PowerShell, CMD, Git Bash, WSL). | *Same core terminal engine.* |
| **Git Dashboard** | :white_check_mark: Local repository tracking, branch detection, ahead/behind upstream counts, staged/unstaged file change list. | Visual SVG commit history graph, stash manager, tag manager, merge/cherry-pick assistants, analytics. |
| **Project Launcher** | :white_check_mark: Single-step program / folder launches. | Multi-step automated sequences (IDE + Terminal script + URL + Folder). |
| **Version Dashboard** | :white_check_mark: Automatic probing and path resolution for 12 developer toolchains. | *Same.* |
| **Command Snippets** | :white_check_mark: Local snippet repository, categorization, favorite pinning, and 1-click terminal insertion. | Cross-device cloud sync *(planned)*. |
| **Settings Hub** | :white_check_mark: All 13 configuration sections and local theme persistence in SQLite. | *Same.* |

---

## 3. Technical Initialization (`FREE_STATUS`)

When Developer Cockpit launches on a machine without an activated license file (`license.enc`), the backend and frontend initialize to the default `FREE_STATUS` contract (`src/types/license.ts`):

```typescript
export const FREE_STATUS: LicenseStatus = {
  edition: "free",
  state: "free",
  email: null,
  issuedAt: null,
  expiresAt: null,
  keyMasked: null,
  lastVerifiedAt: null,
  graceDaysLeft: null,
  reason: null,
};
```

All capability queries (`useFeature()`) evaluate against this status object, instantly enabling all Free capabilities while rendering upgrade paths on Pro-gated views.
