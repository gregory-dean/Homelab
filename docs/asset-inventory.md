# Asset Inventory

This document tracks the systems deployed within the homelab environment.

---

| Asset Name | Role | Operating System | Purpose | Notes |
|------------|------|------------------|--------|------|
| DC-01 | Domain Controller | Windows Server | Active Directory authentication | Core identity service |
| WINCLIENT-01 | Workstation | Windows 10 / 11 | User endpoint simulation | Generates Windows logs |
| KALI-01 | Attack System | Kali Linux | Offensive security testing | Runs attack tools |
| UBUNTU-01 | Linux Server | Ubuntu Server | Linux administration and services | Generates Linux logs |
| SIEM-01 | Monitoring Platform | TBD | Log aggregation and analysis | Splunk or Elastic |

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
