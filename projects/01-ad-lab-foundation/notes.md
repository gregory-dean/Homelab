## Phase 1 Notes

3/15/2026 - 11:45 PM |
Started homelab foundation setup on Windows 11 host.
Confirmed VirtualBox is installed and functioning.
Created local folder structure for ISOs, screenshots, notes, and exports.
Collected installation media for Windows Server, Windows client, Kali Linux, and Ubuntu Server.

3/16/2026 - 1:43 PM |
Configured VirtualBox host-only network for internal lab communication.
Selected 192.168.56.0/24 as the internal subnet.
Chose to use static addressing for critical systems to simplify documentation and troubleshooting.

3/16/2026 - 1:51 PM |
Defined an internal lab subnet of 192.168.56.0/24.
Reserved static IP addresses for core systems to support later Active Directory, SIEM ingestion, and troubleshooting workflows.

3/16/2026 - 4:01 PM |
Validated communication across the VirtualBox host-only network between the Windows 11 host and DC-01. Temporary ICMP echo rules were enabled on DC-01 to confirm Layer 3 connectivity, then disabled again after testing. This confirmed that host-only networking was functioning as intended and that earlier ping failures were caused by Windows Firewall behavior rather than an addressing or routing issue.

3/16/2026 - 4:10 PM |
Deployed Windows Server VM as DC-01.
Assigned NAT for outbound internet access and host-only networking for internal lab communication.
Configured static IP 192.168.56.10 on the internal lab network.
