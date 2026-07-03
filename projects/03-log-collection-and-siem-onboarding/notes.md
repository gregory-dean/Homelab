# Build Notes - Log Collection and SIEM Onboarding

## 2026-03-25
- Created SIEM-01 in VirtualBox for Phase 3.
- Assigned 4 vCPU, 8 GB RAM, a 100 GB dynamically allocated disk, and configured NAT + Host-Only adapters to match the lab design.
- Installed Ubuntu Server on SIEM-01 and set the hostname to siem-01.
- Kept the system off-domain and prepared it as the dedicated Wazuh server for monitoring and detection.
- Configured baseline networking on SIEM-01.
- Left the NAT adapter on DHCP, assigned the LAB adapter static IP 192.168.56.50/24, and pointed internal DNS to DC-01 at 192.168.56.10.
- Validated SIEM-01 network connectivity across the lab.
- Confirmed successful communication with DC-01, WINCLIENT-01, UBUNTU-01, and KALI-01 over the host-only network.
- Applied initial hardening on SIEM-01 before installing Wazuh.
- Updated Ubuntu, confirmed time sync, configured UFW, and limited inbound access to the lab subnet only.
- Installed the Wazuh central components on SIEM-01.
- Deployed the Wazuh indexer, server, and dashboard on the new VM using the single-node lab design.

## 2026-03-26
- Verified the Wazuh services started correctly on SIEM-01.
- Confirmed access to the dashboard over HTTPS and saved the generated admin credentials for lab use.

## 2026-03-27
- Installed the Wazuh agent on DC-01 and connected it to SIEM-01.
- Extended Windows telemetry by preparing the system for Sysmon, PowerShell, and Defender log forwarding.
- Installed Sysmon on DC-01 and updated the Wazuh agent configuration.
- Added Sysmon, PowerShell Operational, and Windows Defender Operational channels to improve visibility for detection use cases.
