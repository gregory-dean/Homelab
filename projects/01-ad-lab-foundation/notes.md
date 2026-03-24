3/23/2026 - 8:15 PM |
Started Phase 2 - Identity and Access.
Confirmed all four Phase 1 systems remain present in VirtualBox with NAT + Host-Only adapters intact.
Created pre-change snapshots for DC-01 and WINCLIENT-01 before Active Directory deployment.

3/24/2026 - 5:00 AM |
Prepared DC-01 for Active Directory.
Verified hostname, confirmed static host-only IP 192.168.56.10, installed current Windows updates, and renamed network adapters to NAT and LAB for clarity.

3/24/2026 - 5:34 AM |
Installed Active Directory Domain Services and DNS on DC-01.
Promoted the server as the first domain controller for a new forest using lab.lan with NetBIOS name LAB.

3/24/2026 - 5:57 AM |
Validated AD post-promotion state on DC-01.
Confirmed the lab.lan zone exists in DNS, verified AD management consoles are present, and configured DC-01 to use its own internal DNS service on the LAB adapter.

3/24/2026 - 6:35 AM |
Created a basic OU structure to support identity management and future GPO targeting.
Added Servers, Workstations, Users, Groups, and Admins OUs.

3/24/2026 - 7:00 AM |
Created initial security groups and test identities in Active Directory.
Added a dedicated labadmin account for administrative tasks and kept privileged membership limited to only what was required for the lab.
