# Lab Architecture

## Overview
This homelab is designed to simulate a small enterprise security environment using VirtualBox on a Windows 11 host.

## Core Systems
- DC-01 - Windows Server 2022 domain controller running Active Directory Domain Services and DNS
- WINCLIENT-01 - Windows 11 Pro domain-joined workstation used for user authentication and policy validation
- KALI-01 - Kali Linux attack simulation machine kept off-domain for future offensive testing
- UBUNTU-01 - Ubuntu Server kept off-domain for future Linux administration, monitoring, and log generation
- SIEM-01 - Ubuntu Server running Wazuh for log aggregation, detection, and alerting

## Goals
- Simulate enterprise user and administrator activity
- Generate realistic Windows and Linux logs
- Practice vulnerability management workflows
- Test attack techniques in a controlled environment
- Develop detection and investigation skills

## Planned Expansion
- Firewall or segmentation layer
- IDS/IPS tooling
- Additional Linux services
- More realistic user activity simulation
- Centralized vulnerability reporting
