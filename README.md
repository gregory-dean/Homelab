# Homelab

This is a full guide with detailed documentation of the 10 inch homelab I've built. Currently my stack is OPNsense for the firewalls and Proxmox nodes for hosting my VMs.

I use this for all cybersecurity related projects and some personal projects like self hosting or game servers.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="images/diagrams/topology-dark.png">
  <img alt="Homelab topology" src="images/diagrams/topology-light.png">
</picture>

![Front](images/rack/01-rack-front.jpg)

The switch in this cabinet is unmanaged, so it can't do VLANs. Everything plugged into it shares one flat network, and the edge firewall never sees traffic between two devices on that switch. To keep the lab separate from the rest of the house I run a VXLAN overlay between the two Proxmox nodes and a second OPNsense install that only routes the lab. The first firewall gets the house onto the internet. The second one keeps the lab, including the attack box, away from the house network.

## Hosts

| Name | Device | Role | Address |
| ---- | ------ | ---- | ------- |
| Sirius | M720q i5-8400T | OPNsense, physical edge | `10.10.10.1` |
| Polaris | M720q i5-9500T | Proxmox, primary | `10.10.10.11` |
| Vega | M715q Ryzen 3 PRO 2200GE | Proxmox, secondary | `10.10.10.12` |
| Sol | Ryzen 7 5800X desktop | Command center | `10.10.10.154` |
| Lyra | Archer AX6000 | Access point | `10.10.10.2` |
| gw-01 | VM on Polaris | OPNsense, lab router | `10.10.10.3` |
| dc-01 | VM on Polaris | AD DS | `10.30.10.10` |
| winclient-01 | VM on Polaris | Windows 11 Pro, domain joined | `10.30.20.20` |
| siem-01 | VM on Polaris | Wazuh | `10.30.10.50` |
| ubuntu-01 | VM on Vega | Ubuntu Server, off domain | `10.30.10.40` |
| kali-01 | VM on Vega | Kali, off domain | `10.30.30.30` |

## Docs

The docs describe the lab as it exists right now: what I bought, how it's addressed, and why the network is shaped the way it is.

- [Architecture](docs/architecture.md)
- [Hardware](docs/hardware.md)
- [Network](docs/network.md)

## Guides

The guides are how I'd build this again, written so you can follow along on your own hardware. The names and part numbers in this README are what I actually run. The guides use placeholders so they aren't tied to my desk.

- [Lab layout](guides/layout.md)
- [Firewall](guides/firewall.md)
- [Hypervisor](guides/hypervisor.md)
- [Access point](guides/access-point.md)
- [Command center](guides/command-center.md)
- [Lab guests](guides/lab-guests.md)
