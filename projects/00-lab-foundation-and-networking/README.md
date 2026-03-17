# Project 00 – Lab Foundation and Networking

## Overview

This project documents the initial buildout of my cybersecurity homelab foundation. The goal of this phase was to prepare the local workspace, organize installation media, configure VirtualBox networking, deploy the first virtual machines, and assign static IP addresses for the internal lab network.

This phase establishes the base environment that later phases will build on for Active Directory, attack simulation, log collection, and defensive monitoring.

---

## Objectives

- Build the initial VirtualBox-based lab environment
- Organize required ISO files and project documentation
- Configure a host-only network for internal lab communication
- Create the initial lab virtual machines
- Assign static IP addresses based on a planned addressing scheme
- Verify basic internal connectivity between systems
- Document the environment in a clean and repeatable way

---

## Environment Summary

### Host system
- Windows 11 desktop
- Oracle VirtualBox
- Project workspace stored on local disk

### Virtual machines created
- `DC-01` – Windows Server 2022
- `WINCLIENT-01` – Windows 11
- `KALI-01` – Kali Linux
- `UBUNTU-01` – Ubuntu Server

---

## Project Workspace

The first step was creating a clean folder structure for ISOs, notes, screenshots, and exports.

![Project workspace](../../assets/images/phase-1-foundation/00-windows-host-project-folder.png)

---

## Installation Media

All required ISO files were collected before deployment.

![ISO files collected](../../assets/images/phase-1-foundation/02-iso-files-collected.png)

---

## VirtualBox Network Configuration

A host-only adapter was configured to provide internal lab communication on the `192.168.56.0/24` network.

![Host-only adapter configuration](../../assets/images/phase-1-foundation/03-virtualbox-network-manager-host-only.png)

---

## IP Addressing Plan

The lab followed a simple static addressing plan to keep systems predictable and easy to document.

![IP addressing plan](../../assets/images/phase-1-foundation/05-ip-scheme.png)

### Planned IP assignments
| System | Planned IP |
|---|---|
| Domain Controller | 192.168.56.10 |
| Windows Client | 192.168.56.20 |
| Kali Linux | 192.168.56.30 |
| Ubuntu Server | 192.168.56.40 |
| SIEM Platform | 192.168.56.50 |

---

## DC-01 Build

The first server deployed in the lab was `DC-01`, which will later support identity and directory services in future phases.

### VM creation
![DC-01 VM creation](../../assets/images/phase-1-foundation/06-dc-01-new-vm-creation-summary.png)

### Static IPv4 configuration
![DC-01 static IP configuration](../../assets/images/phase-1-foundation/09-ipv4-config-dc-01.png)

### IP verification
![DC-01 ipconfig](../../assets/images/phase-1-foundation/24-dc-01-ipconfig.png)

---

## WINCLIENT-01 Build

The Windows client workstation was created to serve as the first user endpoint in the lab.

### VM creation
![WINCLIENT-01 VM creation](../../assets/images/phase-1-foundation/12-winclient-vm-creation-screen.png)

### Static IPv4 configuration
![WINCLIENT-01 static IP configuration](../../assets/images/phase-1-foundation/14-winclient-01-ipv4-config.png)

### IP verification
![WINCLIENT-01 ipconfig](../../assets/images/phase-1-foundation/25-winclient-01-ipconfig.png)

---

## KALI-01 Build

Kali Linux was deployed as the offensive testing and attack simulation system.

### VM creation
![KALI-01 VM creation](../../assets/images/phase-1-foundation/15-kali-01-install-summary.png)

### Interface verification
![KALI-01 ip a](../../assets/images/phase-1-foundation/18-kali-01-ip-a.png)

### Hostname verification
![KALI-01 hostname](../../assets/images/phase-1-foundation/19-kali-01-hostname.png)

---

## UBUNTU-01 Build

Ubuntu Server was added as a general-purpose Linux server for future services, testing, and infrastructure expansion.

### VM creation
![UBUNTU-01 VM creation](../../assets/images/phase-1-foundation/20-ubuntu-01-creation-screen.png)

### Static IPv4 configuration
![UBUNTU-01 static IP configuration](../../assets/images/phase-1-foundation/21-ubuntu-01-ipv4-config.png)

### Hostname and interface verification
![UBUNTU-01 verification](../../assets/images/phase-1-foundation/23-ubuntu-01-ip-a-hostnamectl.png)

---

## Current Lab State

At the end of this phase, all primary systems were created and running in VirtualBox.

![All lab machines running](../../assets/images/phase-1-foundation/31-virtualbox-all-machines-running.png)

---

## Key Outcomes

- Built the first four core virtual machines
- Established a documented host-only lab subnet
- Assigned static internal IPs to each system
- Verified core interface configuration across Windows and Linux systems
- Created a repeatable foundation for later phases

---

## Notes

This phase intentionally focused on infrastructure preparation and internal networking only. Identity services, domain join operations, SIEM deployment, logging, and attack simulation will be added in later projects.

Some connectivity tests showed expected limitations between certain systems at this stage, especially where host firewalls or default OS settings prevented ICMP replies. Those screenshots are better documented in troubleshooting notes rather than the main build walkthrough.

---

## Next Phase

Next, the lab will expand into identity and access configuration, likely beginning with Active Directory setup and Windows client domain integration.
