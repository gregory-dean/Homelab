# Images

Photos and screenshots from the lab as it runs. The PNGs under `diagrams/` are the sheets used in the README and docs.

| Folder | Contents |
| ------ | -------- |
| `diagrams/` | Dark and light PNG exports |
| `rack/` | Photos of the T1 cabinet |
| `hardware/` | Photos of the hosts, the NIC, and the riser |
| `proxmox/` | Cluster UI, taken from Sol |
| `sirius/` | Sirius OPNsense UI, taken from Sol |
| `gw-01/` | gw-01 OPNsense UI, taken from Sol |
| `lyra/` | Lyra Archer UI, taken from Sol |

## Diagrams

| File | What it shows |
| ---- | ------------- |
| `topology-dark.png` / `topology-light.png` | Physical path, Core hosts, lab overlay |
| `segmentation-dark.png` / `segmentation-light.png` | Walls, gates, allow and deny |
| `rack-dark.png` / `rack-light.png` | Front elevation and port map |
| `dns-dark.png` / `dns-light.png` | The two DNS zones |

## Rack

| File | Shot |
| ---- | ---- |
| `01-rack-front.jpg` | Loaded T1, switch through Vega |
| `02-rack-side.jpg` | Side and rear, power leads labeled |

## Hardware

| File | Shot |
| ---- | ---- |
| `01-m720q-front.jpg` | ThinkCentre Tiny, front |
| `02-sirius-open.jpg` | 8400T open, riser and card seated |
| `03-i350-and-riser.jpg` | 10Gtek I350 and Wobeater 01AJ940 |

## Proxmox

Taken from Sol at `https://10.10.10.11:8006`.

| File | Page |
| ---- | ---- |
| `01-cluster-tree.jpg` | Datacenter search, both nodes in the tree |
| `02-polaris-summary.jpg` | polaris → Summary |
| `03-vega-summary.jpg` | vega → Summary |
| `04-sdn-zone.jpg` | Datacenter → SDN → Zones |
| `05-sdn-vnets.jpg` | Datacenter → SDN → VNets |
| `06-storage.jpg` | Datacenter → Storage |
| `07-sdn-vnet-firewall.jpg` | Datacenter → SDN → VNet Firewall |

## Sirius

Taken from Sol at `https://10.10.10.1`.

| File | Page |
| ---- | ---- |
| `01-dashboard.jpg` | Lobby → Dashboard |
| `02-lan-rules.jpg` | Firewall → Rules → LAN |
| `03-source-nat.jpg` | Firewall → NAT → Source NAT |
| `04-routes-status.jpg` | System → Routes → Status |
| `05-gateways.jpg` | System → Gateways → Configuration |
| `06-unbound-forwarding.jpg` | Services → Unbound DNS → Query Forwarding |
| `07-dnsmasq-hosts.jpg` | Services → Dnsmasq → Hosts |

## gw-01

Taken from Sol at `https://10.10.10.3`. The WAN on this box is Core, not the ISP.

| File | Page |
| ---- | ---- |
| `01-dashboard.jpg` | Lobby → Dashboard |
| `02-wan-rules.jpg` | Firewall → Rules → WAN |
| `03-labatk-rules.jpg` | Firewall → Rules → LABATK |
| `04-disable-reply-to.jpg` | Firewall → Settings → Advanced |
| `05-source-nat.jpg` | Firewall → NAT → Source NAT |

## Lyra

Taken from Sol at `https://10.10.10.2`.

| File | Page |
| ---- | ---- |
| `01-network-map.jpg` | Network Map, static `10.10.10.2` |
