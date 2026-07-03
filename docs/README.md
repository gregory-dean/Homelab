# Documentation Index

Central reference for the cybersecurity homelab repository.

## Reference Docs

| Document | Description |
|----------|-------------|
| [lab-architecture.md](lab-architecture.md) | System roles, network design, and expansion plans |
| [asset-inventory.md](asset-inventory.md) | Asset table and IP addressing plan (single source of truth) |
| [roadmap.md](roadmap.md) | Phase checklist (single source of truth) |
| [troubleshooting.md](troubleshooting.md) | Issues encountered and resolutions |

## Projects

| Phase | Folder | Status |
|-------|--------|--------|
| — | [_template](../projects/_template/) | Scaffold for new projects |
| 1 | [Lab Foundation and Networking](../projects/01-lab-foundation-and-networking/) | Complete |
| 2 | [AD Identity and Access](../projects/02-ad-identity-and-access/) | Complete |
| 3 | [Log Collection and SIEM Onboarding](../projects/03-log-collection-and-siem-onboarding/) | In progress |
| 4 | [Vulnerability Management Workflow](../projects/04-vulnerability-management-workflow/) | Planned |
| 5 | [Attack Simulation and Detection](../projects/05-attack-simulation-and-detection/) | Planned |
| 6 | [Linux Hardening and Monitoring](../projects/06-linux-hardening-and-monitoring/) | Planned |

## Conventions

- **Screenshots:** Store under `projects/NN-<name>/images/` and reference from project READMEs as `images/<filename>`. Each active project has an `images/README.md` checklist.
- **Build logs:** Timestamped entries in each project's `notes.md` using the format below.
- **Credentials:** Store in a password manager or local secure store. Never commit passwords, API keys, or screenshots showing credentials. Review images before pushing to the repository.

### Notes Format

```markdown
# Build Notes - <Project Name>

## YYYY-MM-DD
- entry
```
