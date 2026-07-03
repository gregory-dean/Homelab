# Project 03 – Log Collection and SIEM Onboarding

## Overview

This project documents the Monitoring and Detection phase of my cybersecurity homelab build. The goal is to deploy a central SIEM platform, onboard Windows and Linux log sources, validate ingestion, and build initial detections.

This phase builds on the networking foundation from Project 01 and the Active Directory environment from Project 02.

**Status:** In progress — SIEM deployed, DC-01 agent onboarded with Sysmon. Linux log onboarding and detection work remain.

---

## Objectives

- Deploy a dedicated SIEM VM (`SIEM-01`) on the lab network
- Install and configure Wazuh central components
- Onboard Windows systems with the Wazuh agent
- Extend Windows telemetry with Sysmon and additional event channels
- Onboard Linux systems for syslog and auth log collection
- Validate log ingestion across the lab
- Build initial detections and map activity to MITRE ATT&CK

---

## Lab Systems Used

- `SIEM-01` – Ubuntu Server (Wazuh indexer, server, dashboard)
- `DC-01` – Windows Server 2022 (Wazuh agent, Sysmon)
- `WINCLIENT-01` – Windows 11 (planned agent onboarding)
- `UBUNTU-01` – Ubuntu Server (planned agent onboarding)
- `KALI-01` – Kali Linux (off-domain, future log source)

---

## Project Steps

### SIEM-01 Deployment

1. Created `SIEM-01` in VirtualBox (4 vCPU, 8 GB RAM, 100 GB disk)
2. Installed Ubuntu Server and set hostname to `siem-01`
3. Configured networking: NAT on DHCP, LAB adapter at `192.168.56.50/24`, DNS pointed to `DC-01`
4. Validated connectivity to all lab systems over the host-only network
5. Applied baseline hardening (updates, time sync, UFW limited to lab subnet)

### Wazuh Installation

1. Installed Wazuh indexer, server, and dashboard on `SIEM-01` (single-node design)
2. Verified services started and dashboard accessible over HTTPS
3. Saved generated admin credentials locally (not stored in this repository)

### DC-01 Agent Onboarding

1. Installed Wazuh agent on `DC-01` and connected to `SIEM-01`
2. Installed Sysmon on `DC-01`
3. Updated agent configuration to collect Sysmon, PowerShell Operational, and Windows Defender Operational channels

### Remaining Work

- Install Wazuh agents on `WINCLIENT-01` and `UBUNTU-01`
- Validate log ingestion in the Wazuh dashboard
- Build initial detection rules
- Map test activity to MITRE ATT&CK techniques

---

## Testing and Validation

- Confirmed `SIEM-01` reaches all lab systems on `192.168.56.0/24`
- Verified Wazuh services are running and dashboard is accessible
- DC-01 agent connected to the Wazuh manager

Ingestion validation and detection testing are pending.

---

## Findings

- A dedicated off-domain SIEM VM keeps monitoring infrastructure separate from the AD domain
- UFW hardening before Wazuh install limits exposure to the lab subnet only
- Sysmon significantly extends Windows telemetry beyond default Security event logs

---

## Challenges

See [docs/troubleshooting.md](../../docs/troubleshooting.md) for issues encountered in earlier phases that may apply during agent deployment.

---

## Screenshots

Screenshots for this phase are stored in [images/](images/) as work progresses. See [images/README.md](images/README.md) for the checklist.

---

## Skills Demonstrated

- SIEM platform deployment
- Linux server hardening
- Wazuh agent configuration
- Windows event log forwarding
- Sysmon deployment

---

## Future Improvements

- Onboard remaining endpoints
- Create custom Wazuh detection rules
- Document alert triage workflow
- Add MITRE ATT&CK mapping for test scenarios

---

## Build Notes

Detailed timestamped build log: [notes.md](notes.md)
