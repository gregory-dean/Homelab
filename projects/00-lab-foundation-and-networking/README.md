# Project 00 - Lab Foundation and Networking

## Overview

This project documents the first completed phase of the cybersecurity homelab build.

The goal of this phase was to establish the core infrastructure required for later phases of the lab. This included preparing the Windows 11 host environment, configuring Oracle VirtualBox networking, deploying the initial virtual machines, assigning static internal IP addresses, validating connectivity between systems, and documenting the build process along the way.

This project serves as the foundation for the rest of the homelab roadmap, including Active Directory, monitoring and detection, vulnerability management, and attack simulation.

---

## Objectives

- Prepare the Windows 11 host for lab development
- Confirm Oracle VirtualBox installation and functionality
- Configure the host-only network for internal VM communication
- Define a structured internal IP addressing scheme
- Deploy the initial lab virtual machines
- Assign static IP addresses to core systems
- Validate host-to-host communication across the internal subnet
- Document screenshots, notes, and troubleshooting findings

---

## Environment

### Host System
- **Host OS:** Windows 11
- **Hypervisor:** Oracle VirtualBox

### Core Virtual Machines
- **DC-01** - Windows Server
- **WINCLIENT-01** - Windows client workstation
- **KALI-01** - Kali Linux attack machine
- **UBUNTU-01** - Ubuntu Server

---

## Network Design

The lab uses a dual-adapter model for each virtual machine.

### Adapter 1 - NAT
Used for:
- internet access
- package installation
- system updates
- downloading tools and dependencies

### Adapter 2 - Host-Only Adapter
Used for:
- internal lab communication
- predictable static addressing
- isolated testing between virtual machines

### Internal Lab Subnet
- **Subnet:** `192.168.56.0/24`

### Planned IP Addressing
| System | IP Address |
|--------|-----------|
| DC-01 | 192.168.56.10 |
| WINCLIENT-01 | 192.168.56.20 |
| KALI-01 | 192.168.56.30 |
| UBUNTU-01 | 192.168.56.40 |
| SIEM-01 | 192.168.56.50 |

---

## Work Completed

### 1. Host Preparation
- Created the local project workspace on the Windows 11 host
- Organized folders for ISOs, notes, screenshots, and exports
- Verified Oracle VirtualBox installation

### 2. VirtualBox Network Configuration
- Created the VirtualBox host-only adapter
- Configured the internal lab subnet
- Disabled DHCP for cleaner static IP management

### 3. Infrastructure Planning
- Defined the internal IP addressing scheme
- Assigned system roles for each virtual machine
- Prepared the base structure for future lab phases

### 4. Virtual Machine Deployment
Deployed the following systems:
- `DC-01`
- `WINCLIENT-01`
- `KALI-01`
- `UBUNTU-01`

### 5. System Configuration
- Assigned hostnames to each machine
- Configured static IP addressing on the internal interface
- Verified host identity and interface configuration

### 6. Connectivity Validation
- Tested communication across the host-only network
- Confirmed each system could reach intended lab peers
- Documented connectivity validation with screenshots and notes

### 7. Documentation and Troubleshooting
- Recorded build notes during setup
- Captured screenshots throughout the process
- Documented issues encountered and their resolutions

---

## Validation Summary

Phase 1 was considered complete after the following conditions were met:

- VirtualBox networking was configured successfully
- All four core virtual machines were deployed
- Internal static IP addresses were assigned correctly
- Hostnames matched the documented system roles
- Host-to-host communication was tested successfully
- Notes and screenshots were captured for the completed work

---

## Key Deliverables

- Internal lab subnet established
- Core VM infrastructure deployed
- Static IP plan documented
- Connectivity testing completed
- Troubleshooting entries created
- Screenshot evidence captured for each major milestone

---

## Screenshots


![UBUNTU-01 IP and Hostname](../../assets/images/phase-1-foundation/23-ubuntu-01-ip-a-hostnamectl.png)

![All Lab Machines Running](../../assets/images/phase-1-foundation/31-virtualbox-all-machines-running.png)
