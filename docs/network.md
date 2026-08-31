# Network

Addresses, DNS, NIC map, and the patch panel.

![Segmentation](../images/diagrams/architecture-network.jpg)

The wall-and-gate view of the same path is on [architecture](architecture.md).

## Core (Core)

Physical LAN on the LS108GP. Domain `home.lan`. Sirius is DHCP (Dnsmasq) and DNS (Unbound).

| Address | Name | Device |
| ------- | ---- | ------ |
| `sirius.home.lan` | sirius | M720q i5-8400T, OPNsense |
| `lyra.home.lan` | lyra | Archer AX6000 |
| `gw-01.home.lan` | gw-01 | OPNsense VM on Polaris |
| `polaris.home.lan` | polaris | M720q i5-9500T, Proxmox |
| `vega.home.lan` | vega | M715q Ryzen 3, Proxmox |
| `sol.home.lan` | sol | Ryzen 7 desktop, DHCP reservation on Sirius |
| `<core>.100` to `<core>.199` | DHCP pool | Phones, laptops, anything else on Core |

DHCP options: gateway `sirius.home.lan`, DNS `sirius.home.lan`, NTP `sirius.home.lan`, domain `home.lan`. Sol stays on DHCP. Sirius holds a Dnsmasq reservation so Sol always gets `sol.home.lan`. Unbound listens on 53. Dnsmasq listens on 53053 and registers lease names. Unbound forwards `home.lan` to it.

## Lab networks

Virtual. Routed by gw-01. Domain `lab.lan` on `dc-01`.

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

DHCP on each lab net is `x.100` to `x.199` from gw-01.

Workstations use `192.0.2.10` for DNS. Kali uses `203.0.113.1`.

Sirius Unbound forwards `lab.lan` to `192.0.2.10`. gw-01 Unbound does the same. gw-01 DHCP option 6 on LABSRV and LABEP is `192.0.2.10`. LABATK stays on `203.0.113.1`.

## Sirius NIC assignment

10Gtek Intel I350, driver `igb`, plus the onboard NIC.

| Port | Role |
| ---- | ---- |
| I350 port 1 | WAN to the ISP modem |
| I350 port 2 | LAN to the switch, `sirius.home.lan` |
| I350 ports 3 and 4 | unused |
| Onboard | unused |

I confirm names (`igb0` and so on) at the console by plugging one cable at a time. The bracket is labeled to match.

## Routes and NAT

On Sirius:

- `192.0.2.0/24` via `gw-01.home.lan`
- `198.51.100.0/24` via `gw-01.home.lan`
- `203.0.113.0/24` via `gw-01.home.lan`
- Hybrid source NAT for Core (automatic) and the lab prefixes (manual) to WAN

On gw-01: no outbound NAT. Default route `sirius.home.lan`.

## Firewall intent

Sirius LAN, first match. Default “allow LAN to any” is disabled. Access to the GUI and SSH is the Sol rule, not a listen-on-LAN bind.

1. Core to Sirius TCP/UDP 53 (Unbound)
2. Core to Sirius UDP 123 (NTP)
3. Core to Sirius ICMP
4. Sol to Sirius TCP 443 and 22
5. Sol to Polaris and Vega TCP 8006, 22, 5900 to 5999
6. Sol to gw-01 TCP 443 and 22
7. Sol to the lab prefixes any
8. Core to any destination not Core and not the lab prefixes (house internet)
9. Lab prefixes to any destination not Core (lab internet)
10. Deny the rest inbound to Sirius

gw-01 is the zone break:

- Sol may enter the lab
- Other Core clients may not
- Sirius ICMP to gw-01 for route monitoring
- Sirius DNS (TCP/UDP 53) to `dc-01` so Unbound can answer lab names on Core
- Lab nets may not initiate to Core
- `labatk` may reach `labsrv` and `labep` (that is the point)

## Patch panel and switch

Rear of the panel is permanent. Front cords are 0.5 ft Cat6A to the switch. Rear runs are 2 ft Cat6A.

| Panel | Switch | Device |
| ----- | ------ | ------ |
| 1 | 1 | Sirius LAN |
| 2 | 2 | Polaris |
| 3 | 3 | Vega |
| 4 | 4 | Sol |
| 5 | 5 | Lyra |
| 6 to 12 | 6 to 8 | open |

LS108GP: Extend mode off. PoE Auto Recovery off. Lyra uses its own power brick, so PoE is unused.

## VXLAN

Zone id `lab` on both Proxmox nodes. Peers `polaris.home.lan` and `vega.home.lan`. MTU 1450. VNets `labsrv`, `labep`, `labatk`. I do not create extra Linux bridges for the lab. A bridge with no NIC would be local to one node.
