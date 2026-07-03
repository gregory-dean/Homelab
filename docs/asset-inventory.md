# Asset Inventory

This document is the single source of truth for systems deployed in the homelab and their IP addressing plan.

---

| Asset Name | Role | Operating System | IP Address | Notes |
|------------|------|------------------|------------|-------|
| DC-01 | Domain Controller / DNS | Windows Server 2022 | 192.168.56.10 | Hosts lab.lan; Wazuh agent and Sysmon installed |
| WINCLIENT-01 | Domain-Joined Workstation | Windows 11 Pro | 192.168.56.20 | Joined to lab.lan |
| KALI-01 | Attack System | Kali Linux | 192.168.56.30 | Remains off-domain |
| UBUNTU-01 | Linux Server | Ubuntu Server | 192.168.56.40 | Remains off-domain |
| SIEM-01 | Monitoring Platform | Ubuntu Server | 192.168.56.50 | Wazuh indexer, server, and dashboard deployed |

---

## Future Assets

Additional systems may include:

- Vulnerable web applications
- Additional Linux servers
- Security monitoring nodes
- Firewall or gateway systems
