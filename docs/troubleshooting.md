# Troubleshooting Guide

This document tracks issues actually encountered while building the cybersecurity homelab in VirtualBox on a Windows 11 host. It is intended to document real configuration problems, how they were identified, and how they were resolved during the build process.

---

## VirtualBox VM Could Not Communicate with Other Lab Systems

### Symptoms
- Ping requests failed between lab machines
- Host-only network communication did not work as expected
- Systems appeared online but could not reach each other internally

### Likely Cause
- Incorrect VirtualBox adapter configuration
- Host-only adapter not configured properly
- Static IP settings not yet applied correctly
- Firewall behavior affecting ICMP testing

### Checks Performed
- Verified each VM had the expected VirtualBox adapter layout
- Confirmed Adapter 1 was set to NAT for internet access
- Confirmed Adapter 2 was set to Host-Only Adapter for internal lab communication
- Confirmed each VM was using the correct static IP on the `192.168.56.0/24` network
- Tested connectivity with `ping`
- Reviewed interface settings from inside each VM

### Resolution
- Confirmed the lab uses two adapters per VM:
  - **NAT** for outbound internet access
  - **Host-only** for internal lab traffic
- Assigned static IP addresses to the host-only interfaces:
  - `DC-01` → `192.168.56.10`
  - `WINCLIENT-01` → `192.168.56.20`
  - `KALI-01` → `192.168.56.30`
- Verified subnet mask was consistent across systems:
  - `255.255.255.0` or `/24`
- Re-tested communication after correcting adapter and IP settings

### Notes
This was one of the most important early troubleshooting steps because correct host-only networking is required before domain services, attacks, logging, and lateral testing can function reliably.

---

## Windows Server Ping / Network Behavior Was Inconsistent

### Symptoms
- Ping tests timed out unexpectedly
- Network communication worked only after specific configuration changes
- Connectivity appeared partially functional and then failed again

### Likely Cause
- Windows firewall rules affecting ICMP echo traffic
- Network profile or interface configuration not fully aligned with the lab design

### Checks Performed
- Verified static IP settings on the Windows Server host-only adapter
- Confirmed the server was on the expected internal subnet
- Tested ping from other VMs
- Compared behavior before and after Windows networking changes

### Resolution
- Adjusted Windows network-related settings until internal ping traffic worked again
- Confirmed the `.10` address belonged to `DC-01` and not the host machine
- Re-tested connectivity after the change
- Documented that ping behavior can be misleading if firewall rules are still blocking ICMP even when interface configuration is otherwise correct

### Notes
A machine may still be correctly configured even if `ping` fails at first. Firewall behavior should always be considered before assuming the IP configuration is wrong.

---

## Host-Only Adapter IP Configuration Needed Manual Verification

### Symptoms
- Unclear which interface should receive the static internal IP
- Difficulty locating the correct static IP settings page
- Risk of assigning the lab IP to the NAT adapter instead of the host-only adapter

### Likely Cause
- Multi-adapter VM design creates ambiguity during initial setup
- NAT and host-only interfaces serve different purposes and must not be mixed

### Checks Performed
- Reviewed VirtualBox adapter assignments
- Confirmed NAT was intended for internet connectivity
- Confirmed host-only was intended for lab-only communication
- Matched interface behavior with actual addresses and routes

### Resolution
- Used the host-only adapter for the internal lab IPs
- Left the NAT adapter responsible for internet connectivity
- Avoided assigning lab static IP information to the wrong interface

### Notes
This is a repeatable issue in multi-NIC lab builds and should always be verified before moving into domain setup or logging configuration.

---

## Kali Linux Host-Only Interface Did Not Have a Saved Connection Profile

### Symptoms
- `eth1` appeared in `ip a` but did not have an IPv4 address
- Attempting to modify `"Wired connection 2"` failed
- `nmcli` returned:
  - `Error: unknown connection 'Wired connection 2'`

### Likely Cause
- The host-only interface existed, but NetworkManager had no saved connection profile for it
- Only the NAT adapter connection had been created automatically

### Checks Performed
- Used `ip a` to identify interfaces
- Confirmed:
  - `eth0` had a `10.0.2.x` address and default route
  - `eth1` was present but had no IPv4 address
- Used `ip route` to confirm NAT traffic was leaving through `eth0`
- Used `nmcli connection show` and confirmed only:
  - `Wired connection 1` mapped to `eth0`
  - no existing saved profile for `eth1`

### Resolution
- Created a new NetworkManager connection for `eth1`
- Assigned the host-only static IP:
  - `192.168.56.30/24`
- Brought the new connection up and verified the interface configuration afterward

### Notes
This was an important troubleshooting step because the interface physically existed, but could not be modified until a connection profile was manually created.

---

## Kali Linux Static IP Assignment Needed Interface Validation First

### Symptoms
- Uncertainty about which Kali interface should receive the static lab IP
- Concern about breaking internet access by assigning the address to the wrong NIC

### Likely Cause
- Kali had both NAT and host-only adapters present
- Only one interface should carry the internal lab address

### Checks Performed
- Reviewed `ip a`
- Reviewed `ip route`
- Identified:
  - `eth0` as NAT
  - `eth1` as host-only

### Resolution
- Kept `eth0` on DHCP for NAT/internet access
- Assigned `192.168.56.30/24` to `eth1`
- Left the default route on `eth0`

### Notes
This preserves internet access while also enabling internal communication with the other lab systems.

---

## Windows Client Initial Build / Local Setup Friction

### Symptoms
- Windows setup prompts did not match expectations
- No obvious skip option appeared during setup
- Questions came up around Microsoft account usage vs local account usage

### Likely Cause
- Windows installation flow varies depending on edition, build, and network state
- Consumer-oriented setup screens can push Microsoft account sign-in even in a lab build

### Checks Performed
- Evaluated whether setup could be skipped or bypassed
- Considered switching from Microsoft account to local account after installation
- Confirmed this phase was focused on machine build and internal networking, not domain join yet

### Resolution
- Continued setup using the most practical path available
- Deferred domain join to a later project phase
- Focused on completing machine deployment and internal network configuration first
- Set up a local user account after installation process

### Notes
For this homelab, the important part was getting `WINCLIENT-01` installed, named correctly, and placed on the internal lab network. Domain join belongs later in the roadmap.

---

## Group Policy Management View Was Correct but the Wrong Tree Level Was Selected

### Symptoms
- The expected **Workstations** OU was not visible at first in **Group Policy Management**
- It was unclear where to create and link the workstation baseline GPO

### Likely Cause
- The view was focused at the **Domains** level rather than the expanded domain OU structure
- The required OU was either not expanded yet or needed to be refreshed

### Checks Performed
- Reviewed the left navigation tree in **Group Policy Management**
- Confirmed the domain `lab.lan` was present
- Expanded the domain hierarchy further and refreshed the view

### Resolution
- Navigated into the correct domain tree level under `lab.lan`
- Located the **Workstations** OU
- Continued with creation and linking of the `Phase-2-Workstation-Baseline` GPO in the correct location

### Notes
This was a navigation issue rather than a Group Policy configuration failure.

---

## DNS Forwarder Configuration Included Unwanted Legacy IPv6 Entries

### Symptoms
- The DNS Forwarders window showed a valid forwarder along with multiple failed IPv6 entries
- Validation displayed `OK` for the primary upstream resolver but errors for several additional entries

### Likely Cause
- Legacy placeholder IPv6 forwarders were present in the DNS server configuration
- These were not needed for the lab and created unnecessary validation noise

### Checks Performed
- Opened **DNS Manager** on `DC-01`
- Reviewed the **Forwarders** tab
- Confirmed `192.168.0.1` validated successfully
- Observed failed entries for old `fec0::` style IPv6 resolver addresses

### Resolution
- Kept `192.168.0.1` as the upstream DNS forwarder
- Removed the invalid legacy IPv6 forwarder entries
- Reconfirmed the forwarder list contained only the intended upstream resolver

### Notes
This preserved internal AD DNS authority on `DC-01` while still allowing external name resolution through the upstream network.

---

## Windows Client Could Not Join the Domain Because the Wrong Edition Was Installed

### Symptoms
- The **Domain** option could not be selected in the Computer Name / Domain Changes window
- Windows displayed a message stating the machine could not join a domain because of the installed edition
- The client appeared to be Windows 11, but still behaved like an unsupported edition for domain join

### Likely Cause
- `WINCLIENT-01` was installed with **Windows Home** instead of **Windows 11 Pro**
- Windows Home does not support Active Directory domain join

### Checks Performed
- Opened the domain join dialog and confirmed the **Domain** option was unavailable
- Reviewed the error shown by Windows during the join attempt
- Verified the installed edition before rebuilding the client

### Resolution
- Rebuilt `WINCLIENT-01` using **Windows 11 Pro**
- Reconfirmed the computer name as `WINCLIENT-01`
- Reapplied the lab network settings after the rebuild
- Returned to the domain join process only after confirming the correct edition was installed

### Notes
This issue was not caused by DNS or Active Directory itself. The domain join was blocked by the Windows edition on the client.

---

## Windows Client DNS Was Pointing to the Wrong Resolver for Domain Join

### Symptoms
- `WINCLIENT-01` had valid IP configuration on the lab network but domain-related name resolution was unreliable
- Domain join preparation was incomplete even though the lab adapter had the correct static IP
- The client still showed upstream DNS information from the NAT side instead of using the domain controller for internal lookups

### Likely Cause
- The workstation was still using an upstream resolver instead of the domain controller for Active Directory DNS
- In a multi-adapter setup, NAT-side DNS can appear correct for internet access while still breaking AD name resolution

### Checks Performed
- Reviewed `ipconfig /all` on `WINCLIENT-01`
- Confirmed the lab adapter was using `192.168.56.20/24`
- Verified the VM had both a NAT adapter and a host-only lab adapter
- Confirmed the domain controller lab IP was `192.168.56.10`

### Resolution
- Set the **LAB** adapter on `WINCLIENT-01` to use the domain controller as DNS
- Used `192.168.56.10` as the preferred DNS server
- Left NAT in place for internet access, but did not use it for AD DNS lookups
- Retested domain resolution and continued with the join process

### Notes
In this lab, internal clients should use `DC-01` for DNS. Public DNS or router DNS should not be used directly on a domain-joined workstation.

---

## Domain Join Failed Because the Administrative Account Required a Password Change

### Symptoms
- The domain join process reached the credentials stage successfully
- Windows returned the following error when attempting to join the domain:
  - `The user's password must be changed before signing in.`

### Likely Cause
- The account used for the join, such as `labadmin`, was configured to require a password change at next logon
- That requirement prevented the account from being used for the domain join operation

### Checks Performed
- Confirmed the client could reach the domain and that name resolution was functioning
- Reviewed the exact error shown during the join attempt
- Identified that the issue was account-related rather than network-related

### Resolution
- Opened **Active Directory Users and Computers** on `DC-01`
- Located the `labadmin` account
- Reset the password as needed
- Cleared the **User must change password at next logon** requirement
- Retried the domain join using the updated account credentials

### Notes
This was a useful distinction because the domain itself was reachable. The failure was caused by account state, not by DNS, connectivity, or the workstation build.

---

## Ubuntu DNS Test Initially Failed Because the Command Was Entered Incorrectly

### Symptoms
- Running `resolvectl` returned `Unknown command verb`
- DNS validation did not run even though the hostname was entered

### Likely Cause
- The `query` verb was omitted from the command syntax

### Checks Performed
- Compared the entered command against the documented syntax
- Confirmed the hostname itself was formatted correctly

### Resolution
- Re-ran the command with the correct syntax:
  - `resolvectl query dc-01.lab.lan --legend=no`

### Notes
This was a simple syntax issue, but it was important to correct before deeper DNS troubleshooting.

---

## Ubuntu DNS Validation Initially Failed Because It Used the Wrong DNS Server

### Symptoms
- `resolvectl query dc-01.lab.lan --legend=no` returned `Name 'dc-01.lab.lan' not found`
- Ubuntu could not resolve internal AD hostnames even though the domain controller was reachable on the lab network

### Likely Cause
- `UBUNTU-01` was using the upstream resolver `192.168.0.1` instead of the domain controller for internal lab DNS
- The upstream resolver had no knowledge of the internal `lab.lan` zone

### Checks Performed
- Ran `resolvectl status` and confirmed the active DNS server was `192.168.0.1`
- Confirmed this resolver was attached to the NAT side of the VM
- Compared the failed `resolvectl` result with the expected lab DNS design

### Resolution
- Verified the domain controller lab IP was `192.168.56.10`
- Used explicit server-targeted validation with `dig` to confirm internal DNS was working on `DC-01`
- Confirmed successful responses for:
  - `dig @192.168.56.10 dc-01.lab.lan`
  - `dig @192.168.56.10 winclient-01.lab.lan`
- Kept `UBUNTU-01` off-domain while still validating internal DNS through the domain controller

### Notes
This confirmed that Ubuntu did not need to join the domain in Phase 2. It only needed to be able to resolve internal lab systems through `DC-01`.

---

## Domain Controller Registered Both Lab and NAT Addresses in Internal DNS

### Symptoms
- Explicit DNS testing returned more than one A record for `dc-01.lab.lan`
- The domain controller hostname resolved to both the internal lab IP and the NAT IP

### Likely Cause
- `DC-01` registered multiple interface addresses in AD-integrated DNS
- Both the host-only adapter and NAT adapter were present during DNS registration

### Checks Performed
- Queried the domain controller directly with:
  - `dig @192.168.56.10 dc-01.lab.lan`
- Confirmed the response included:
  - `192.168.56.10`
  - `10.0.2.15`
- Compared the result with the expected internal-only lab resolution path

### Resolution
- Confirmed that explicit DNS queries to `DC-01` were functioning
- Continued using `192.168.56.10` as the intended internal lab address for DNS validation and internal communication
- Documented the duplicate record behavior for cleanup awareness during later refinement

### Notes
This did not block Phase 2 completion, but it is worth tracking because internal clients should ideally prefer the host-only lab address for AD-related communication.
