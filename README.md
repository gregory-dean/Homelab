# Cybersecurity Homelab

This repository documents the design, build process, and security experiments performed in my personal cybersecurity homelab.

The goal of this project is to build a controlled enterprise-style lab for practicing system administration, networking, detection engineering, vulnerability management, and attack simulation while documenting each phase in a structured and public-facing way.

The lab is continuously evolving as I expand the infrastructure and add new security tools and testing scenarios.

---

## Current Status

| Phase | Project | Status |
|-------|---------|--------|
| 1 | [Lab Foundation and Networking](projects/01-lab-foundation-and-networking/) | Complete |
| 2 | [AD Identity and Access](projects/02-ad-identity-and-access/) | Complete |
| 3 | [Log Collection and SIEM Onboarding](projects/03-log-collection-and-siem-onboarding/) | In progress |
| 4 | [Vulnerability Management Workflow](projects/04-vulnerability-management-workflow/) | Planned |
| 5 | [Attack Simulation and Detection](projects/05-attack-simulation-and-detection/) | Planned |
| 6 | [Linux Hardening and Monitoring](projects/06-linux-hardening-and-monitoring/) | Planned |

See [docs/roadmap.md](docs/roadmap.md) for the full phase checklist.

---

## Objectives

- Build a realistic enterprise-style security lab
- Practice both offensive and defensive security concepts
- Simulate attack activity in a controlled environment
- Generate and analyze logs across Windows and Linux systems
- Document the full build process for learning and portfolio development

---

## Lab Architecture

This lab is built on a Windows 11 host using Oracle VirtualBox.

![Cybersecurity Homelab Network Diagram](docs/images/homelab-diagram.png)

### Internal Lab Network

- Subnet: `192.168.56.0/24`
- VirtualBox NAT used for outbound internet access
- VirtualBox Host-Only Adapter used for internal lab communication

See [docs/lab-architecture.md](docs/lab-architecture.md) for system roles and design goals.  
See [docs/asset-inventory.md](docs/asset-inventory.md) for the full asset table and IP addressing plan.

---

## Tools and Technologies

### Platforms and Operating Systems
- Windows 11
- Windows Server 2022
- Ubuntu Server
- Kali Linux

### Infrastructure
- Oracle VirtualBox
- Host-only networking
- NAT networking
- Active Directory Domain Services
- Group Policy
- DNS

### Security Tooling
- Wazuh
- Sysmon
- Nmap
- Wireshark
- Nessus or OpenVAS
- Metasploit
- Burp Suite

---

## Documentation

See [docs/README.md](docs/README.md) for the full documentation index.
