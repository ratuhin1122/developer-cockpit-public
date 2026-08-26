# Security Policy

Developer Cockpit takes product security seriously.

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 0.1.x   | :white_check_mark: |

## Reporting a Vulnerability

If you discover a potential security vulnerability in Developer Cockpit or its Plugin SDK, please report it responsibly:

1. **Do not create public GitHub issues** for security-sensitive bugs.
2. Email full vulnerability details, steps to reproduce, and impact assessments to: `security@developercockpit.app`.
3. We will acknowledge receipt of your vulnerability report within 48 hours and provide updates regarding resolution and coordinated disclosure.

## Security Architecture Principles

- **Zero-Password Storage:** SSH credentials and remote server passwords are never stored in application databases or logs.
- **DPAPI Token Encryption:** Sensitive local tokens are encrypted using Windows Data Protection API (DPAPI).
- **Sandboxed Plugins:** Third-party plugins execute in isolated sandbox contexts with access mediated via the versioned `CockpitApi`.
