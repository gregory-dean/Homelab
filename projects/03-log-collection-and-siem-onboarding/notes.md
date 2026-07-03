3/25/2026 - 6:18 PM |
Created SIEM-01 in VirtualBox for Phase 3.
Assigned 4 vCPU, 8 GB RAM, a 100 GB dynamically allocated disk, and configured NAT + Host-Only adapters to match the lab design.

3/25/2026 - 6:36 PM |
Installed Ubuntu Server on SIEM-01 and set the hostname to siem-01.
Kept the system off-domain and prepared it as the dedicated Wazuh server for monitoring and detection.

3/25/2026 - 6:55 PM |
Configured baseline networking on SIEM-01.
Left the NAT adapter on DHCP, assigned the LAB adapter static IP 192.168.56.50/24, and pointed internal DNS to DC-01 at 192.168.56.10.

3/25/2026 - 7:03 PM |
Validated SIEM-01 network connectivity across the lab.
Confirmed successful communication with DC-01, WINCLIENT-01, UBUNTU-01, and KALI-01 over the host-only network.

3/25/2026 - 8:42 PM |
Applied initial hardening on SIEM-01 before installing Wazuh.
Updated Ubuntu, confirmed time sync, configured UFW, and limited inbound access to the lab subnet only.

3/25/2026 - 10:52 PM |
Installed the Wazuh central components on SIEM-01.
Deployed the Wazuh indexer, server, and dashboard on the new VM using the single-node lab design.

3/26/2026 - 12:33 AM |
Verified the Wazuh services started correctly on SIEM-01.
Confirmed access to the dashboard over HTTPS and saved the generated admin credentials for lab use.

3/27/2026 - 10:12 PM |
Installed the Wazuh agent on DC-01 and connected it to SIEM-01.
Extended Windows telemetry by preparing the system for Sysmon, PowerShell, and Defender log forwarding.

3/27/2026 - 11:13 PM |
Installed Sysmon on DC-01 and updated the Wazuh agent configuration.
Added Sysmon, PowerShell Operational, and Windows Defender Operational channels to improve visibility for detection use cases.
