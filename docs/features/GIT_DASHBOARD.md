# Git Dashboard & Advanced Version Control

> **Status:** VERIFIED (Phase 6 Implementation)  
> **Source Locations:** `src/modules/git/`, `src-tauri/src/commands/git/`, `src-tauri/src/db.rs` (Migration v4)

---

## Overview

The **Git Dashboard** provides a unified repository workspace for tracking local repositories, inspecting branches, visualizing commit histories through an interactive SVG graph, managing stashes and tags, executing merge and cherry-pick operations with conflict guidance, and reviewing contributor analytics.

---

## Problem It Solves

Full-scale Git GUI clients are often memory-heavy Electron apps or overly complex tools that clutter simple day-to-day repository inspection. The Git Dashboard delivers instant repository health, visual branch history, and common Git workflows in the same cockpit window.

---

## Capabilities

- **Repository Tracking:** Track multiple local repositories with automatic root discovery (`git rev-parse --show-toplevel`).
- **Working Tree Health:** Displays active branch, upstream tracking status, ahead/behind commit counts, and staged/unstaged file modifications with change badges (`modified`, `added`, `deleted`, `renamed`, `untracked`, `conflict`).
- **Visual SVG Commit Graph (`CommitLog.tsx`, `graph-layout.ts`):** Pure SVG multi-branch topological commit graph with commit hash, author, date, and commit message.
- **Commit Detail Panel:** Full commit inspector showing parent hashes, author information, commit message body, and changed file lists.
- **Stash Manager (`StashManager.tsx`):** List, create, apply, pop, and drop stashes.
- **Tag Manager (`TagManager.tsx`):** View, create annotated/lightweight tags, and delete tags.
- **Merge & Cherry-Pick Assistants:** Guided branch merge and commit cherry-pick workflows with conflict detection, continue, and abort operations.
- **Repository Analytics (`AnalyticsPanel.tsx`):** Aggregates commit velocity, weekly activity trends, and author contribution distributions.

---

## User Workflow

1. Open the Git module via the left icon rail or `Ctrl+5`.
2. Click **"Track Repo"** and select or paste any subfolder within a Git repository.
3. Select a repository from the left master list to inspect working tree status, ahead/behind indicators, and modified files.
4. Switch to the **History**, **Stashes**, **Tags**, or **Analytics** tabs for advanced operations.

---

## Technical Implementation

- **Database (`src-tauri/src/db.rs`):**
  - Table: `repos` (Migration v4) storing tracked repository paths.
- **Backend Handlers (`src-tauri/src/commands/git/`):**
  - `git_repo_status`: Runs `git status --porcelain=v2` and branch queries.
  - `git_log`: Formats commit histories using custom `git log` format templates.
  - `git_stash_*`, `git_tag_*`, `git_checkout`, `git_merge`, `git_cherry_pick`: Structured CLI execution.
  - `git_analytics`: Parses `git log --shortstat` to compute author and time distribution metrics.

---

## Free / Pro Availability

- **Free Edition:** Basic Git Dashboard (repository tracking, branch status, ahead/behind counters, staged/unstaged changed file status).
- **Pro Edition:** Advanced Git Suite (SVG visual commit graph, commit details, stash manager, tag manager, merge/cherry-pick assistants, and repository analytics).

---

## Limitations

- **Git Dependency:** Requires a standard `git` CLI installed on system `PATH`.
- **Large Repositories:** Repository analytics on massive mono-repos (100,000+ commits) may take several seconds to compute.

---

## Future Improvements

- [ ] Interactive rebase assistant.
- [ ] Side-by-side visual file diff viewer with syntax highlighting.
