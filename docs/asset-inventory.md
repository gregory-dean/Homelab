# Asset Inventory

This document tracks the systems deployed within the homelab environment.

---

| Asset Name | Role | Operating System | Purpose | Notes |
|------------|------|------------------|---------|------|
| DC-01 | Domain Controller / DNS | Windows Server 2022 | Active Directory authentication and internal DNS | Hosts lab.lan |
| WINCLIENT-01 | Domain-Joined Workstation | Windows 11 | User endpoint simulation | Joined to lab.lan |
| KALI-01 | Attack System | Kali Linux | Offensive security testing | Remains off-domain in Phase 2 |
| UBUNTU-01 | Linux Server | Ubuntu Server | Linux administration and services | Remains off-domain in Phase 2 |
| SIEM-01 | Monitoring Platform | TBD | Log aggregation and analysis | Planned for Phase 3 |

---

## IP Addressing Plan

| System | Planned IP |
|------|------|
| Domain Controller | 192.168.56.10 |
| Windows Client | 192.168.56.20 |
| Kali Linux | 192.168.56.30 |
| Ubuntu Server | 192.168.56.40 |
| SIEM Platform | 192.168.56.50 |

---

## Future Assets

Additional systems may include:

• Vulnerable web applications  
• Additional Linux servers  
• Security monitoring nodes  
• Firewall or gateway systems
