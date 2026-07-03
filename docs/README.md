# Documentation Index

Central reference for the cybersecurity homelab repository.

## Reference Docs

| Document | Description |
|----------|-------------|
| [lab-architecture.md](lab-architecture.md) | System roles and expansion plans |
| [asset-inventory.md](asset-inventory.md) | Asset table and IP addressing plan |
| [roadmap.md](roadmap.md) | Phase checklist (single source of truth) |
| [troubleshooting.md](troubleshooting.md) | Issues encountered and resolutions |

## Projects

| Phase | Folder | Status |
|-------|--------|--------|
| 00 | [Lab Foundation and Networking](../projects/00-lab-foundation-and-networking/) | Complete |
| 01 | [AD Lab Foundation](../projects/01-ad-lab-foundation/) | Complete |
| 02 | [Log Collection and SIEM Onboarding](../projects/02-log-collection-and-siem-onboarding/) | In progress |
| 03 | [Vulnerability Management Workflow](../projects/03-vulnerability-management-workflow/) | Planned |
| 04 | [Attack Simulation and Detection](../projects/04-attack-simulation-and-detection/) | Planned |
| 05 | [Linux Hardening and Monitoring](../projects/05-linux-hardening-and-monitoring/) | Planned |

## Report Templates

- [Incident report template](../reports/templates/incident-report-template.md)
- [Vulnerability assessment template](../reports/templates/vuln-assessment-template.md)

## Conventions

- **Screenshots:** Store under `assets/images/phase-N-<topic>/` and reference from project READMEs.
- **Build logs:** Timestamped entries in each project's `notes.md`.
- **Credentials:** Store in a password manager or local secure store. Never commit passwords, API keys, or screenshots showing credentials. Review images before pushing to the repository.
