# Inventory

A row is here because the system exists, not because it might exist later.

## Physical

| Name | Role | Hardware | Address |
| ---- | ---- | -------- | ------- |
| Sirius | Edge firewall | M720q i5-8400T, 16 GB, 256 GB NVMe, I350 | `sirius.home.lan` |
| Polaris | Proxmox node | M720q i5-9500T, 32 GB, 512 GB NVMe | `polaris.home.lan` |
| Vega | Proxmox node | M715q Ryzen 3 PRO 2200GE, 32 GB, 250 GB SSD | `vega.home.lan` |
| Sol | Command center | Ryzen 7 5800X, 32 GB, 3080 Ti | `sol.home.lan` |
| Lyra | Access point | Archer AX6000 | `lyra.home.lan` |
| Switch | Core L2 | TP-Link LS108GP | none |
| Panel | Patch | 12 port keystone, 0.5U | none |

## Guests

| Name | Node | Role | Address |
| ---- | ---- | ---- | ------- |
| gw-01 | Polaris | Lab router, OPNsense | `gw-01.home.lan`, `192.0.2.1`, `198.51.100.1`, `203.0.113.1` |
| dc-01 | Polaris | Windows Server, AD DS for `lab.lan` | `192.0.2.10` |
| winclient-01 | Polaris | Windows 11 Pro, domain joined | `198.51.100.20` |
| siem-01 | Polaris | Ubuntu Server, Wazuh | `192.0.2.50` |
| ubuntu-01 | Vega | Ubuntu Server, off domain | `192.0.2.40` |
| kali-01 | Vega | Kali, off domain | `203.0.113.30` |
