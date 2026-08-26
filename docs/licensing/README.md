# Developer Cockpit — Commercial & Licensing Architecture

This directory contains technical documentation for Developer Cockpit's commercial architecture, capability-driven feature flags, and cryptographic licensing engine.

---

## Documentation Index

1. **[Commercial Architecture Overview](./OVERVIEW.md)**  
   *Architectural philosophy: Capability-driven gating vs. direct edition checks, and licensing subsystem components.*

2. **[Free Edition Architecture](./FREE_EDITION.md)**  
   *Zero registration, offline-first developer experience, included feature set, and default initialization contracts.*

3. **[Pro Edition Architecture](./PRO_EDITION.md)**  
   *Unlocked Pro capabilities, multi-service orchestration, container workflows, and perpetual vs. subscription modeling.*

4. **[License Manager & Cryptographic Validation](./LICENSE_MANAGER.md)**  
   *Ed25519 digital signature validation, DCK.v1 token schema, Windows DPAPI encryption, and 30-day offline policy.*

5. **[Feature Flag & Capability Architecture](./FEATURE_FLAGS.md)**  
   *Centralized Feature Catalog, FeatureId taxonomy, capability gating, and step-by-step guide to adding new flags.*

6. **[Commercial Architecture & Enterprise Integration Guide](./COMMERCIAL_ARCHITECTURE.md)**  
   *eCommerce webhooks (Stripe / LemonSqueezy), online verification seams, and how to extend or replace the licensing engine.*
