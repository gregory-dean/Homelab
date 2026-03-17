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
