# Build Notes - Lab Foundation and Networking

## 2026-03-15
- Started homelab foundation setup on Windows 11 host.
- Confirmed VirtualBox is installed and functioning.
- Created local folder structure for ISOs, screenshots, notes, and exports.
- Collected installation media for Windows Server, Windows client, Kali Linux, and Ubuntu Server.

## 2026-03-16
- Configured VirtualBox host-only network for internal lab communication.
- Selected 192.168.56.0/24 as the internal subnet.
- Chose to use static addressing for critical systems to simplify documentation and troubleshooting.
- Defined an internal lab subnet of 192.168.56.0/24.
- Reserved static IP addresses for core systems to support later Active Directory, SIEM ingestion, and troubleshooting workflows.
- Validated communication across the VirtualBox host-only network between the Windows 11 host and DC-01. Temporary ICMP echo rules were enabled on DC-01 to confirm Layer 3 connectivity, then disabled again after testing. This confirmed that host-only networking was functioning as intended and that earlier ping failures were caused by Windows Firewall behavior rather than an addressing or routing issue.
- Deployed Windows Server VM as DC-01.
- Assigned NAT for outbound internet access and host-only networking for internal lab communication.
- Configured static IP 192.168.56.10 on the internal lab network.

## 2026-03-17
- Deployed Windows client VM as WINCLIENT-01.
- Configured dual-network design with NAT and host-only adapters.
- Assigned static lab IP 192.168.56.20.
- Deferred domain join until Identity and Access phase.
- Deployed Kali Linux as KALI-01 for attack simulation and offensive tooling.
- Attached both NAT and host-only adapters.
- Configured internal lab IP addressing to support controlled connectivity testing.
- Deployed Ubuntu Server as UBUNTU-01 for Linux services, testing, and future log generation.
- Configured host-only networking for isolated lab traffic and NAT for package updates.
- Assigned internal IP 192.168.56.40.
- Validated internal lab connectivity over the host-only network.
- Confirmed IP assignments for all four core systems.
- Observed that Windows firewall behavior may affect ICMP testing and should be documented in troubleshooting notes if ping results are inconsistent.
