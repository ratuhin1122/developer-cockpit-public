# Free vs. Pro Editions & Capability Gating

> **Status:** VERIFIED (Phase 1, Phase 12 & Licensing Implementation)  
> **Source Locations:** `src/services/license/feature-catalog.ts`, `src/store/license-store.ts`, `src/components/pro/`

---

## Overview

Developer Cockpit is architected around an open-core commercial model offering a robust, fully-functional **Free Edition** alongside an advanced **Pro Edition** designed for multi-service orchestration, container workflows, and team development.

---

## Feature Matrix Summary

| Subsystem / Feature | Free Edition | Pro Edition |
| :--- | :---: | :---: |
| **Modern Terminal** (Tabs, splits, themes, zoom, search) | :white_check_mark: Full | :white_check_mark: Full |
| **Basic Git Dashboard** (Status, branches, ahead/behind) | :white_check_mark: Full | :white_check_mark: Full |
| **Project Launcher (Single-Step)** | :white_check_mark: Full | :white_check_mark: Full |
| **Version Dashboard (12 Tools)** | :white_check_mark: Full | :white_check_mark: Full |
| **Command Snippets Library (Local)** | :white_check_mark: Full | :white_check_mark: Full |
| **Settings Hub & Theming** | :white_check_mark: Full | :white_check_mark: Full |
| **Workspace Manager** (Snapshot & restore) | :x: Gated | :white_check_mark: Full |
| **Project Launcher (Multi-Step Sequences)** | :x: Gated | :white_check_mark: Full |
| **Port Manager** (Win32 inspection, tree kill, restart) | :x: Gated | :white_check_mark: Full |
| **Advanced Git Suite** (Graph, stashes, tags, merge/cherry-pick) | :x: Gated | :white_check_mark: Full |
| **Docker Workspace & Docker Doctor** | :x: Gated | :white_check_mark: Full |
| **SSH Manager** (Profile storage & shell connect) | :x: Gated | :white_check_mark: Full |
| **Plugin System & Extensibility SDK** | :x: Gated | :white_check_mark: Full |

---

## Technical Enforcement

Gating is evaluated declaratively via `editionSatisfies()` and rendered through `ProGate.tsx` and `ProBadge.tsx` without disrupting core navigation.
