# Firewall

The hostnames and addresses on this page are examples. Core is `10.10.10.0/24`, the lab prefixes are `10.30.10.0/24`, `10.30.20.0/24`, and `10.30.30.0/24`, and the two firewalls are `edge` and `lab-gw`. My real addresses are in [network](../docs/network.md).

There are two OPNsense installs in this design. The edge runs on real hardware and comes first, since nothing else has internet or DHCP until it's up. The lab router is a VM, and it can't exist until the hypervisors are clustered and the VXLAN vnets are built, so the first half of [hypervisor](hypervisor.md) has to happen before the second half of this page.

Everything here matches OPNsense 26.7. Dnsmasq handles DHCP, Source NAT replaced the old Outbound page, and Firewall → Rules is the newer MVC editor. ISC DHCP is end of life, and I don't enable Kea.

Get in the habit of exporting `config.xml` after every session of changes. It's under **System → Configuration → Backups**, and it's the fastest way back if you lock yourself out.

## Edge firewall

The edge is the physical box. WAN comes from the ISP modem and LAN goes into the switch as `10.10.10.1`.

### USB and install

1. On the admin PC, download the current OPNsense amd64 **vga** image (`OPNsense-*-vga-amd64.img.bz2`) from [opnsense.org/download](https://opnsense.org/download/). The DVD ISO written with Rufus in DD mode also works, but vga is the image the project intends for USB.
2. Check the SHA256 of the `.bz2` against the 26.7 release notes, then extract it with 7-Zip.
3. Write the USB with Rufus in **DD Image** mode, not ISO mode.
4. Boot the edge box from the USB in UEFI mode. Skip the config importer and log in as `installer` with the password `opnsense`.
5. Install to the internal disk with UFS, not to the USB stick. Take the recommended swap (8 GB). UFS is the simple choice on a single disk firewall.
6. Set a strong root password, reboot, and pull the USB.

### Assign interfaces

Do this at the console and let the cables tell you which NIC is which. Driver names like `igb0` and `em0` aren't guaranteed to match the order of the ports on the bracket, and guessing wrong here costs you a lot of confusion later.

1. Unplug every Ethernet cable from the box.
2. Plug the modem into the port you want to be WAN and watch the console link status. Whichever device comes up is WAN.
3. Move that cable to the port you want to be LAN and note the device. That's LAN.
4. In menu 1, skip VLANs unless your switch is actually doing them. Assign only WAN and LAN and leave any spare NICs unassigned.
5. In menu 2, set LAN to `10.10.10.1/24`, say no to IPv6, and enable DHCP from `10.10.10.100` to `10.10.10.199`. Keep the GUI on HTTPS.
6. Plug the modem into WAN and the switch into LAN, then connect the admin PC to the switch. It should pull an address in the `10.10.10.1xx` range.

Only one device should be serving DHCP on Core. The modem and the access point both have to stay out of that job.

The GUI is at `https://10.10.10.1` as `root`. Accept the self signed certificate for this host only.

![My edge dashboard](../images/sirius/01-dashboard.jpg)

### Firmware and general

Go to **System → Firmware → Status** and check for updates before you start a long configuration session, then reboot.

Under **System → Settings → General**:

- Hostname `edge`, or whatever you picked
- A domain for Core, for example `home.example.com`
- DNS servers left empty, since Unbound handles resolution
- Allow DNS server list to be overridden by DHCP/PPP on WAN: off
- Do not use the local DNS service as a nameserver: off
- Your timezone
- Prefer IPv4 over IPv6: on

### WAN and LAN

On WAN, use IPv4 via DHCP with IPv6 set to none. Turn on both block bogon networks and block private networks, and don't run a DHCP server on it. If WAN comes up with an RFC1918 address, the modem is still doing NAT and needs to go into bridge mode. And don't publish your public WAN address anywhere, including a repo like this one.

On LAN, set IPv4 static `10.10.10.1/24`, IPv6 none, and leave both block private and block bogon off.

### DHCP (Dnsmasq)

Under **Services → Dnsmasq DNS & DHCP → General**:

- Enable: on
- Listen port `53053`
- Interface: LAN
- Do not forward to system defined DNS servers: on
- DHCP fqdn: on
- DHCP register firewall rules: on
- DHCP default domain: empty, so it uses the Core domain

In **DHCP ranges**, delete any leftover `192.168.1.100` to `199` range and any IPv6 or RA ranges, then set the LAN range to `10.10.10.100` through `10.10.10.199`. The router and DNS options fill in automatically as `10.10.10.1`.

In **DHCP options**, add `ntp-server[42]` = `10.10.10.1` on LAN.

**Hosts** is where reservations live. Add them as each device comes online. Dnsmasq wants colons in the MAC address, not the dashes Windows prints.

| Example name | Address | Notes |
| ------------ | ------- | ----- |
| ap | `10.10.10.2` | Outside the pool |
| lab-gw | `10.10.10.3` | Outside the pool. Add the Proxmox net0 MAC once the VM exists |
| pve-1 | `10.10.10.11` | Outside the pool |
| pve-2 | `10.10.10.12` | Outside the pool |
| admin | `10.10.10.154` | Inside the pool, if the desktop stays on DHCP |

![Dnsmasq hosts on my edge](../images/sirius/07-dnsmasq-hosts.jpg)

### Unbound DNS

Under **Services → Unbound DNS → General**, enable it on port 53, listening on all interfaces, with DNSSEC on. Leave DHCP lease registration off. Confirm the exact label in the 26.7 UI, since the ISC DHCP integration it used to refer to is gone.

For **DNS over TLS**, leave Domain empty so each entry is a catch all:

- `1.1.1.1` port 853, Verify CN `cloudflare-dns.com`
- `9.9.9.9` port 853, Verify CN `dns.quad9.net`

Don't also add a catch all under Query Forwarding, or the two will fight.

**Query Forwarding** is how Unbound answers Core names and PTRs from Dnsmasq:

- The Core domain → `127.0.0.1` port `53053`
- `10.10.10.in-addr.arpa` → `127.0.0.1` port `53053`

Delete any leftover `192.168.1` or `lan.internal` forwards that came with the default image.

Add **Host overrides** under the Core domain for `edge`, `ap`, `lab-gw`, `pve-1`, `pve-2`, and `admin`. They resolve even when a lease has expired or a guest is off.

Under **Advanced → Private Domains**, add the Core domain and the AD lab domain. Without this, rebind protection strips RFC1918 answers for those zones.

Under **System → Settings → Administration → Alternate Hostnames**, add the firewall's FQDN, for example `edge.home.example.com`.

Once the domain controller exists, add a Query Forwarding entry for the AD domain → `10.30.10.10` port 53. Don't add it before then, because Unbound will SERVFAIL on a forwarder that isn't answering. The same day, add the lab router WAN pass from the edge to the DC on TCP/UDP 53. Otherwise the Core deny on `lab-gw` drops that forward and every lab lookup from Core times out.

![Unbound forwarding on my edge](../images/sirius/06-unbound-forwarding.jpg)

### NTP

Leave **Services → Network Time** enabled with the OPNsense pool upstream. Core clients get `10.10.10.1` as their NTP server through DHCP option 42.

### Aliases

Under **Firewall → Aliases**:

| Name | Type | Content |
| ---- | ---- | ------- |
| `ADMIN` | Host(s) | The admin PC, for example `10.10.10.154` |
| `HYPERVISORS` | Host(s) | `10.10.10.11`, `10.10.10.12` |
| `LAB_GW` | Host(s) | `10.10.10.3` |
| `CORE_NET` | Network(s) | `10.10.10.0/24` |
| `LAB_NETS` | Network(s) | `10.30.0.0/16` |
| `HOME_AND_LAB` | Network(s) | `10.10.10.0/24`, `10.30.0.0/16` |
| `HTTPS_SSH` | Port(s) | `443`, `22` |
| `PVE_ADMIN` | Port(s) | `8006`, `22`, `5900:5999` |

`HOME_AND_LAB` exists so that a single inverted match can mean "the internet."

### Static routes

Under **System → Gateways → Configuration**, add a gateway named `LABGW` on interface LAN with IPv4 `10.10.10.3`. Leave upstream off and turn on **Disable Gateway Monitoring** for now. If monitoring stays on before the lab router exists, OPNsense marks the gateway down and withdraws every route that uses it. Leave it disabled until the lab router has an ICMP pass for the edge.

Under **System → Routes → Configuration**, add three routes via `LABGW`:

- `10.30.10.0/24`
- `10.30.20.0/24`
- `10.30.30.0/24`

They don't do anything until the lab router boots, but adding them now means the lab works the moment it does.

![Gateways on my edge](../images/sirius/05-gateways.jpg)

![Route status on my edge](../images/sirius/04-routes-status.jpg)

### NAT

Under **Firewall → NAT → Source NAT**, set the mode to **Hybrid** and add one manual rule: source `LAB_NETS` (`10.30.0.0/16`) out WAN, translated to the WAN address. The lab router doesn't NAT, so lab sources arrive at the edge unchanged and need this rule to get out. The automatic hybrid rules already cover `10.10.10.0/24`. There are no port forwards.

![Source NAT on my edge](../images/sirius/03-source-nat.jpg)

### WAN and LAN rules

WAN needs nothing. The default deny covers inbound, and I don't allow management or ICMP from the internet.

LAN is where the work is. Open **Firewall → Rules** and filter on LAN. In the 26.7 MVC editor, rules are first match with **Quick** on, direction **in**, and version **IPv4**. Source Port is almost always **any**, and the interface has to be **LAN** rather than **any**.

Leave the automatically generated rules alone, meaning anti lockout and DHCP. Add the custom rules below while the default "allow LAN to any" IPv4 rule is still enabled, test that everything works, and only then disable both default LAN allows (IPv4 and IPv6). Click **Apply** each time, or pf keeps running the old set.

A common mistake is setting Source Port to 53 or 123. That's the client's ephemeral port, so match on **Destination Port** instead. And if **This Firewall** is missing from the destination dropdown, **LAN address** does the same job here.

| # | Protocol | Source | Dest | Dest port | Description |
| - | -------- | ------ | ---- | --------- | ----------- |
| auto | | | | | Anti lockout. Leave it. |
| 1 | TCP/UDP | `CORE_NET` | This Firewall | DOMAIN (53) | Core DNS to the edge |
| 2 | UDP | `CORE_NET` | This Firewall | NTP (123) | Core NTP to the edge |
| 3 | ICMP | `CORE_NET` | This Firewall | (none) | Core can ping the edge |
| 4 | TCP | `ADMIN` | This Firewall | `HTTPS_SSH` | Admin to the edge UI and SSH |
| 5 | TCP | `ADMIN` | `HYPERVISORS` | `PVE_ADMIN` | Admin to Proxmox UI, SSH, and VNC |
| 6 | TCP | `ADMIN` | `LAB_GW` | `HTTPS_SSH` | Admin to the lab router UI and SSH |
| 7 | **any** | `ADMIN` | `LAB_NETS` | any | Admin into the lab networks |
| 8 | any | `CORE_NET` | **invert** `HOME_AND_LAB` | any | Core to the internet only |
| 9 | any | `LAB_NETS` | **invert** `CORE_NET` | any | Lab to the internet, not Core |

Gateway on all of these is **None**. Rule 7 has to be protocol **any** rather than TCP, or anything that isn't TCP (ping, for a start) never makes it into the lab.

Rules 1 through 3 matter more than they look. Without them, disabling the default LAN allow kills DNS and NTP for the whole house, including the admin PC you're working from.

Rule 8 is house internet. Core may go anywhere except Core and the lab, which is what keeps WiFi clients from being routed into `10.30.0.0/16`.

Rule 9 is lab internet through the edge NAT. It doesn't stop an attack box from reaching the admin PC, because that traffic never touches the edge. `lab-gw` and the admin PC share the same Layer 2, so the deny for that has to live on `lab-gw`.

![LAN rules on my edge](../images/sirius/02-lan-rules.jpg)

### Admin hardening

Under **System → Settings → Administration**, keep the web UI on HTTPS with listen interfaces set to **All**. OPNsense warns that binding to LAN only can lock you out, and WAN is already default deny, so the admin only LAN rules above are the real control. Turn SSH on with root login permitted, listening on all interfaces on port 22. Leave password login on until the key works.

Paste the admin PC's public key into **System → Access → Users → root → Authorized keys**, then test with `ssh root@10.10.10.1`. Once that logs in without a password prompt, disable password login. Keep a console session open the whole time so a mistake doesn't lock you out.

UPnP stays off. I don't install the plugin at all.

### Edge checks

Before moving on, confirm the following from the admin PC:

- It has its reserved address with gateway and DNS both `10.10.10.1`
- `nslookup` resolves the edge and a hypervisor by name
- The internet works
- `https://10.10.10.1` still loads after the default LAN allow is disabled
- SSH works with the key and rejects passwords

One more thing to understand before the next section. The edge cannot filter the admin PC from `lab-gw`, because they share Core Layer 2 and the edge never sees that traffic. Isolation for the cyber range comes from the other OPNsense.

## Lab router (`lab-gw`)

Create the VM in [hypervisor](hypervisor.md) first, with four virtio NICs: `vmbr0` for Core, then `labsrv`, `labep`, and `labatk`.

The lab router's WAN is Core, and a fresh OPNsense treats WAN like the internet: block private networks on, default deny inbound, and replies pinned to the WAN gateway with `reply-to`. Every Apply, and console menu 2, turns pf back on with those defaults. So create the `ADMIN` alias and a WAN pass for the admin PC before you let pf stay enabled, or your GUI session drops every time you save.

When it does lock you out, unlock it from the `lab-gw` console (Proxmox noVNC, then menu 8), not from the hypervisor host:

```bash
pfctl -d
ifconfig vtnet0
```

`vtnet0` should show `inet 10.10.10.3`. If it shows a pool address between `10.10.10.100` and `.199`, DHCP won and you need to set WAN back to Static. Then from the admin PC open `https://10.10.10.3`, add the admin WAN pass, turn on **Firewall → Settings → Advanced → Disable reply-to**, click Apply, and run `pfctl -e`. Confirm the GUI still loads with pf enabled before you do anything else.

![My lab router dashboard](../images/gw-01/01-dashboard.jpg)

One quirk of the 26.7 MVC rule editor: Source and Destination can't take a raw IP address. Create the aliases first and search by alias name.

### Interfaces

The `vtnet` order follows net0 through net3. Confirm the MACs against the Proxmox NIC list before assigning anything.

- WAN is vtnet0, the Core uplink. Set it **Static** at `10.10.10.3/24` with upstream gateway `10.10.10.1`. No DHCP client, no DHCP server, IPv6 none. Block private networks **off** and block bogons **off**, since this WAN is a private network on purpose.
- OPT1 is vtnet1. Rename it LABSRV, `10.30.10.1/24`, DHCP `10.30.10.100` to `10.30.10.199`.
- OPT2 is vtnet2. Rename it LABEP, `10.30.20.1/24`, DHCP `10.30.20.100` to `10.30.20.199`.
- OPT3 is vtnet3. Rename it LABATK, `10.30.30.1/24`, DHCP `10.30.30.100` to `10.30.30.199`.

In system settings, the hostname is `lab-gw`, the domain is the AD lab domain, and the timezone matches the edge.

Unbound on the lab router listens on the three lab interfaces and forwards everything to `10.10.10.1`. Don't add a domain override for the AD zone until the DC exists.

Under **Firewall → NAT → Source NAT**, set the mode to **Disable**. This box is a router, and the edge does the NAT.

![Source NAT on my lab router](../images/gw-01/05-source-nat.jpg)

Under **Firewall → Settings → Advanced**, turn on **Disable reply-to**. The WAN here and the admin PC share Core Layer 2, and `reply-to` pinned to `WAN_GW` (`10.10.10.1`) forces replies through the edge, which breaks the GUI even when the pass rule matches. The edge keeps `reply-to` because its WAN really is public.

![Disable reply-to on my lab router](../images/gw-01/04-disable-reply-to.jpg)

Add the lab router's net0 MAC to the edge Dnsmasq reservation for `10.10.10.3` so DHCP can never hand it a pool address again.

Turn edge gateway monitoring for `LABGW` back on only after **Interfaces → Diagnostics → Ping** on the edge gets replies from `10.10.10.3`. That needs the edge ICMP pass in the WAN rules below. If monitoring is on and the ping fails, OPNsense marks `LABGW` down and the `10.30` routes disappear.

### Lab router aliases

- `ADMIN`, host, the admin PC
- `EDGE`, host, `10.10.10.1`
- `CORE_NET`, network, `10.10.10.0/24`
- `LABSRV_NET`, network, `10.30.10.0/24`
- `LABEP_NET`, network, `10.30.20.0/24`
- `LABATK_NET`, network, `10.30.30.0/24`
- `DC`, host, `10.30.10.10`
- `SIEM`, host, `10.30.10.50`

### Lab router rules

OPNsense evaluates top down and stops at the first match, so block rules sit above the allows they override. Review the order after saving.

On WAN, the Core uplink:

1. Allow source `ADMIN` to any. This is admin access plus the jump into the lab.
2. Allow source `EDGE` to This Firewall, ICMP, for the edge `LABGW` monitor. Without it, the `CORE_NET` deny below catches the edge too.
3. Allow source `EDGE` to `DC`, TCP/UDP 53, so edge Unbound can reach the DC. Add this when the DC exists.
4. Deny source `CORE_NET` to any. Phones and house devices stay out of the lab.

![WAN rules on my lab router](../images/gw-01/02-wan-rules.jpg)

On LABSRV:

1. Allow to `LABEP_NET` any, for AD, GPO, and SMB
2. Allow to `LABSRV_NET` any, for server to server traffic and SIEM agents
3. Block to `CORE_NET` any
4. Block to `LABATK_NET` any, since servers don't initiate toward the attack box
5. Allow to any, for internet via the edge

On LABEP:

1. Allow to `DC` any, for the first domain join
2. Allow to `SIEM` any, for the Wazuh agent
3. Allow to `LABSRV_NET` any
4. Block to `CORE_NET` any
5. Block to `LABATK_NET` any
6. Allow to any, for internet

On LABATK:

1. Block to `CORE_NET` any. This protects the admin PC, the hypervisors, the AP, and anything personal on Core.
2. Allow to `LABSRV_NET` any
3. Allow to `LABEP_NET` any
4. Allow to any, for tools and updates

![LABATK rules on my lab router](../images/gw-01/03-labatk-rules.jpg)

### DHCP DNS after the DC

Once the domain controller is promoted, go to **Services → Dnsmasq DNS & DHCP → DHCP options** on the lab router and click **+** to add `dns-server[6]` = `10.30.10.10` on LABSRV and LABEP. The options table starts empty, so there's nothing to edit, only add. Leave LABATK alone. The attack box keeps using `10.30.30.1`.

The same day, add query forwarding for the AD zone → `10.30.10.10` in the lab router's Unbound.

### Lab router checks

From the admin PC:

- `ping 10.10.10.3` answers
- `ping 10.30.10.1` answers, which proves the edge static routes and `lab-gw` together
- `ping 10.30.20.1` and `ping 10.30.30.1` answer
- `https://10.10.10.3` opens with pf enabled
- A phone on WiFi cannot open `https://10.10.10.3`

From the edge, a Diagnostics ping to `10.10.10.3` replies, **System → Gateways → Status** shows `LABGW` Online, and **System → Routes → Status** lists the three `10.30` prefixes via `10.10.10.3`.

Two things look wrong but aren't. The `vxlan_*` interfaces on the hypervisors show operstate UNKNOWN, and the lab router can't ping the hypervisor. The datacenter firewall has no ICMP allow, and that's not a broken overlay.

## If something breaks

WAN came up as `192.168.x.x` or something in `10.x`. The modem is still NATing. Put it in bridge mode.

The NIC names don't match the ports on the bracket. Unplug everything and bring one cable up at a time, then label the ports you actually assigned.

House DNS died the moment you disabled "allow LAN to any." Rules 1 through 3 weren't in place first. Turn the default rule back on, add them, and try again.

The `10.30` routes vanished. Gateway monitoring marked `LABGW` down. Disable monitoring until the lab router passes ICMP from the edge, then turn it back on.

The lab router's WAN grabbed a pool address. Set WAN back to static and reserve its MAC on the edge so it can't happen again.

The GUI died when you enabled pf on the lab router. The `reply-to` setting is still on, and WAN shares a Layer 2 with the admin PC. Disable it.

`nslookup` for a lab name through the edge times out, but the DC answers when you ask it directly. The WAN pass from the edge to the DC on TCP/UDP 53 is missing, or it's below the Core deny.

The admin PC can't ping `10.30.10.1`. Check the edge routes, make sure `LABGW` isn't marked down, confirm the lab router's WAN gateway, and confirm NAT is disabled on the lab router.
