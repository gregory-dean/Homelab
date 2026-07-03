# Build Notes - AD Identity and Access

## 2026-03-23
- Started Phase 2 - Identity and Access.
- Confirmed all four Phase 1 systems remain present in VirtualBox with NAT + Host-Only adapters intact.
- Created pre-change snapshots for DC-01 and WINCLIENT-01 before Active Directory deployment.

## 2026-03-24
- Prepared DC-01 for Active Directory.
- Verified hostname, confirmed static host-only IP 192.168.56.10, installed current Windows updates, and renamed network adapters to NAT and LAB for clarity.
- Installed Active Directory Domain Services and DNS on DC-01.
- Promoted the server as the first domain controller for a new forest using lab.lan with NetBIOS name LAB.
- Validated AD post-promotion state on DC-01.
- Confirmed the lab.lan zone exists in DNS, verified AD management consoles are present, and configured DC-01 to use its own internal DNS service on the LAB adapter.
- Created a basic OU structure to support identity management and future GPO targeting.
- Added Servers, Workstations, Users, Groups, and Admins OUs.
- Created initial security groups and test identities in Active Directory.
- Added a dedicated labadmin account for administrative tasks and kept privileged membership limited to only what was required for the lab.
- Documented core Group Policy basics for the new domain.
- Configured domain password policy, set an account lockout threshold of 10, and created a minimal workstation-linked GPO placeholder for future policy growth.
- Prepared WINCLIENT-01 for domain join.
- Confirmed the LAB adapter kept the static 192.168.56.20 address and changed client DNS to 192.168.56.10 so the workstation could resolve the new Active Directory domain.
- Ran into an issue regarding WINCLIENT-01 operating system.
- Created New WINCLIENT-01 machine with Windows 11 Pro
- Joined WINCLIENT-01 to the lab.lan domain using the dedicated labadmin account.
- Moved the computer object into the Workstations OU to align with the Phase 2 policy structure.
- Validated domain authentication on WINCLIENT-01 using the standard greg.dean account.
- Confirmed domain identity, verified logon server resolution to DC-01, and checked that Group Policy processing was active on the domain-joined workstation.
- Used KALI-01 only for Phase 2 validation and documentation support.
- Confirmed it remained off-domain and verified that the domain controller DNS service could resolve core lab hosts from the internal network.
- Validated the new Active Directory DNS service from UBUNTU-01 without joining the system to the domain.
- Kept the Linux server aligned with the roadmap by limiting Phase 2 work to network and name-resolution verification only.
