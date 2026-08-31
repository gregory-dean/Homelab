# Homelab

I run a 10 inch rack on the desk. OPNsense sits at the edge. Two Proxmox nodes hold the guests. This Windows desktop is the command center.

I use it for security work and for things I want to host myself.

![Network](images/diagrams/network.jpg)

![Front](images/rack/01-rack-front.jpg)

The switch in this cabinet cannot do VLANs. Isolation is a VXLAN overlay between the two Proxmox nodes, plus a second OPNsense install that only routes the lab. Sirius never sees traffic between two devices on the same switch. That is why both firewalls exist.

## Hosts

| Name | Device | Role | Address |
| ---- | ------ | ---- | ------- |
| Sirius | M720q i5-8400T | OPNsense, physical edge | `sirius.home.lan` |
| Polaris | M720q i5-9500T | Proxmox, primary | `polaris.home.lan` |
| Vega | M715q Ryzen 3 PRO 2200GE | Proxmox, secondary | `vega.home.lan` |
| Sol | Ryzen 7 5800X desktop | Command center | `sol.home.lan` |
| Lyra | Archer AX6000 | Access point | `lyra.home.lan` |
| gw-01 | VM on Polaris | OPNsense, lab router | `gw-01.home.lan` |
| dc-01 | VM on Polaris | AD DS | `192.0.2.10` |
| winclient-01 | VM on Polaris | Windows 11 Pro, domain joined | `198.51.100.20` |
| siem-01 | VM on Polaris | Wazuh | `192.0.2.50` |
| ubuntu-01 | VM on Vega | Ubuntu Server, off domain | `192.0.2.40` |
| kali-01 | VM on Vega | Kali, off domain | `203.0.113.30` |

Star names stay on the metal. Role names stay on the guests.

## Docs

Reference. Addresses, hardware, and what is actually running.

- [Architecture](docs/architecture.md)
- [Hardware](docs/hardware.md)
- [Network](docs/network.md)
- [Inventory](docs/inventory.md)

## Guides

How I built each piece. Written so you can repeat it.

- [Rack](guides/rack.md)
- [Firewall](guides/firewall.md)
- [Hypervisor](guides/hypervisor.md)
- [Switching](guides/switching.md)
- [Access point](guides/access-point.md)
- [Command center](guides/command-center.md)
- [Lab guests](guides/lab-guests.md)

Editable diagrams live in [`images/diagrams/architecture.drawio`](images/diagrams/architecture.drawio). Open that file in [diagrams.net](https://app.diagrams.net). The Traffic page is the wall-and-gate view of how packets move.
