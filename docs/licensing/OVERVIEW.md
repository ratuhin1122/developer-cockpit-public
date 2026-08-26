# Commercial & Licensing Architecture — Overview

> **Status:** Verified against Developer Cockpit v0.1.0 codebase.

---

## 1. Architectural Philosophy: Capability-Driven Gating

A core architectural principle of Developer Cockpit is that **features are controlled through centralized feature capabilities/flags rather than scattering direct edition checks throughout the application.**

Instead of hardcoding checks like:
```tsx
// ❌ ANTI-PATTERN: Direct edition check scattered in components
if (edition === "pro") {
  renderDockerWorkspace();
}
```

Developer Cockpit routes all feature enablement checks through the **Feature Catalog** and capability queries:
```tsx
// ✅ CAPABILITY-DRIVEN PATTERN: Centralized capability evaluation
const isDockerEnabled = useFeature("docker");

// Or declaratively:
<ProGate feature="docker">
  <DockerWorkspaceView />
</ProGate>
```

```mermaid
flowchart TD
    LicenseState["License Status State\n(edition: 'free' | 'pro')"]
    Catalog["Feature Catalog (feature-catalog.ts)\nDeclares required editions & metadata per feature"]
    Evaluator["editionSatisfies(edition, required)"]
    Store["useFeature(featureId) / isFeatureEnabled(featureId)"]
    Components["UI Components, Navigation Rail, ProGate Wrappers"]

    LicenseState --> Evaluator
    Catalog --> Evaluator
    Evaluator --> Store
    Store --> Components
```

### 1.1 Key Benefits of Capability-Driven Gating
1. **Decoupled Business Logic:** Adding a new feature, transitioning a Pro feature to Free, or creating an intermediate tier (e.g. "Team" or "Enterprise") requires modifying only the central Feature Catalog (`feature-catalog.ts`), not dozens of UI files.
2. **Rich Upgrade Context:** The Feature Catalog attaches descriptive metadata (title, tagline, benefits list) to each feature identifier, allowing the `ProGate` component to render rich, feature-specific upgrade guidance automatically.
3. **Auditability:** Every gateable capability in the application is explicitly enumerated in a single TypeScript union type (`FeatureId`), preventing undocumented or accidental feature gating.

---

## 2. Licensing Subsystem Components

The commercial architecture spans both frontend and backend boundaries:

```mermaid
graph TD
    subgraph Frontend ["Frontend Layer (TypeScript / React)"]
        FCatalog["Feature Catalog (src/services/license/feature-catalog.ts)"]
        LStore["License Store (src/store/license-store.ts)"]
        ProComponents["ProGate, ProBadge, UpgradePage (src/components/pro/)"]
        SettingsUI["License Settings Section (src/modules/settings/sections/license.tsx)"]
    end

    subgraph Backend ["Backend Layer (Rust)"]
        CmdLicense["Tauri Commands (src-tauri/src/commands/license.rs)"]
        Ed25519Val["Ed25519 Validator (src-tauri/src/license/offline.rs)"]
        PolicyEval["Policy Evaluator (src-tauri/src/license/policy.rs)"]
        DPAPIStore["DPAPI Storage Manager (src-tauri/src/license/storage.rs)"]
        SyncTask["Background Sync Task (src-tauri/src/license/sync.rs)"]
    end

    SettingsUI -->|invoke license_activate / deactivate| CmdLicense
    LStore -->|invoke license_status| CmdLicense
    CmdLicense --> Ed25519Val
    CmdLicense --> PolicyEval
    CmdLicense --> DPAPIStore
    CmdLicense --> SyncTask
    
    LStore --> FCatalog
    FCatalog --> ProComponents
```

---

## 3. Documentation Index

For detailed specifications on each aspect of the commercial and licensing architecture, refer to:

- **[FREE_EDITION.md](./FREE_EDITION.md)**: Capabilities, default state, and zero-configuration developer experience.
- **[PRO_EDITION.md](./PRO_EDITION.md)**: Pro features, workflow orchestration, and enterprise value propositions.
- **[FEATURE_FLAGS.md](./FEATURE_FLAGS.md)**: The `FeatureCatalog`, `FeatureId` taxonomy, and gating mechanics.
- **[LICENSE_MANAGER.md](./LICENSE_MANAGER.md)**: Ed25519 token format (`DCK.v1`), cryptographic validation, DPAPI encryption, and the 30-day offline policy.
- **[COMMERCIAL_ARCHITECTURE.md](./COMMERCIAL_ARCHITECTURE.md)**: Payment gateway integration points, future server architecture, and migration guide for enterprise engineering teams.
