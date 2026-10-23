# Network

This is the address book for the lab: Core and lab addresses, DNS, the NIC assignments on Sirius, routes and NAT, firewall rule order, and the patch map.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../images/diagrams/dns-dark.png">
  <img alt="DNS flow" src="../images/diagrams/dns-light.png">
</picture>

The segmentation view of the same network is in [architecture](architecture.md).

## Core (`home.lan`)

Core is the physical LAN on the LS108GP. Its domain is `home.lan`, and Sirius provides both DHCP through Dnsmasq and DNS through Unbound.

> Addresses in this table are examples (`*.lan` names), not the live lab.

| Address | Name | Device |
| ------- | ---- | ------ |
| `sirius.home.lan` | sirius | M720q i5-8400T, OPNsense |
| `lyra.home.lan` | lyra | Archer AX6000 |
| `gw-01.home.lan` | gw-01 | OPNsense VM on Polaris |
| `polaris.home.lan` | polaris | M720q i5-9500T, Proxmox |
| `vega.home.lan` | vega | M715q Ryzen 3, Proxmox |
| `.100` to `.199` | DHCP pool | Phones, laptops, anything else on Core |
| `sol.home.lan` | sol | Ryzen 7 desktop, DHCP reservation on Sirius |

DHCP hands out `sirius.home.lan` as the gateway, DNS server, and NTP server, with `home.lan` as the domain. Sol stays on DHCP, and a Dnsmasq reservation on Sirius makes sure it always gets its reserved address. Unbound listens on port 53. Dnsmasq listens on 53053 and registers lease names, and Unbound forwards the `home.lan` zone to it.

## Lab networks

The lab networks are virtual and routed by gw-01. Lab subnets here use the RFC 5737 documentation ranges as examples: labsrv `192.0.2.0/24`, labep `198.51.100.0/24`, labatk `203.0.113.0/24`. The domain is `lab.lan`, served by `dc-01`.

> Addresses in this table are examples: `*.lan` names and RFC 5737 documentation ranges, not the live lab.

| Address | Name | VNet | Notes |
| ------- | ---- | ---- | ----- |
| `192.0.2.1` | gw-01 | labsrv | Lab server gateway |
| `192.0.2.10` | dc-01 | labsrv | Windows Server, AD DS |
| `192.0.2.40` | ubuntu-01 | labsrv | Ubuntu Server, off domain |
| `192.0.2.50` | siem-01 | labsrv | Ubuntu Server, Wazuh |
| `198.51.100.1` | gw-01 | labep | Endpoint gateway |
| `198.51.100.20` | winclient-01 | labep | Windows 11 Pro, domain joined |
| `203.0.113.1` | gw-01 | labatk | Attack gateway |
| `203.0.113.30` | kali-01 | labatk | Kali, off domain |

gw-01 runs DHCP on each lab network with a pool from `.100` to `.199`.

Workstations use `192.0.2.10` for DNS, and Kali uses `203.0.113.1`. Unbound on both Sirius and gw-01 forwards `lab.lan` to `192.0.2.10`. DHCP option 6 on LABSRV and LABEP also points at `192.0.2.10`, while LABATK stays on `203.0.113.1`.

## Sirius NIC assignment

Sirius has the 10Gtek Intel I350, which uses the `igb` driver, plus the onboard NIC.

| Port | Role |
| ---- | ---- |
| I350 port 1 | WAN to the ISP modem |
| I350 port 2 | LAN to the switch, `sirius.home.lan` |
| I350 ports 3 and 4 | unused |
| Onboard | unused |

I confirmed which `igb` device was which at the console by plugging in one cable at a time, and the bracket is labeled to match.

## Routes and NAT

On Sirius:

- `192.0.2.0/24` (labsrv) via `gw-01.home.lan`
- `198.51.100.0/24` (labep) via `gw-01.home.lan`
- `203.0.113.0/24` (labatk) via `gw-01.home.lan`
- Hybrid source NAT for Core (automatic) and the lab prefixes (manual) out WAN

gw-01 does no outbound NAT. Its default route is `sirius.home.lan`.

## Firewall intent

These are the Sirius LAN rules in order, and the first match wins. The default "allow LAN to any" rule is disabled. Access to the GUI and SSH comes from the Sol rule rather than from binding the listener to LAN, and anti lockout stays on. Anything that doesn't match rules 1 through 9 is denied.

1. Core to Sirius TCP/UDP 53 (Unbound)
2. Core to Sirius UDP 123 (NTP)
3. Core to Sirius ICMP
4. Sol to Sirius TCP 443 and 22
5. Sol to Polaris and Vega TCP 8006, 22, 5900 to 5999
6. Sol to gw-01 TCP 443 and 22
7. Sol to the lab prefixes any
8. Core to any destination not Core and not the lab prefixes (house internet)
9. Lab prefixes to any destination not Core (lab internet)

gw-01 is where the lab is separated from Core:

- Sol may enter the lab
- Other Core clients may not
- Sirius may ping gw-01 for route monitoring
- Sirius may reach `dc-01` on TCP/UDP 53 so Unbound can answer lab names on Core
- Lab networks may not initiate to Core
- `labatk` may reach `labsrv` and `labep`, since attacking those is the whole point of the range

## Patch panel and switch

The rear of the panel is wired once and left alone. Front cords are 0.5 ft Cat6A to the switch, and the rear runs to each device are 2 ft Cat6A.

| Panel | Switch | Device |
| ----- | ------ | ------ |
| 1 | 1 | Sirius LAN |
| 2 | 2 | Polaris |
| 3 | 3 | Vega |
| 4 | 4 | Sol |
| 5 | 5 | Lyra |
| 6 to 12 | 6 to 8 | open |

On the LS108GP, Extend mode and PoE Auto Recovery are both off. Lyra runs from its own power brick, so PoE goes unused.

## VXLAN

The zone is `lab` on both Proxmox nodes, with peers `polaris.home.lan` and `vega.home.lan` and an MTU of 1450. The vnets are `labsrv`, `labep`, and `labatk`. I don't create extra Linux bridges for the lab, because a bridge with no physical NIC only exists on one node.
