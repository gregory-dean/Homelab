# Hypervisor

The two nodes here are `pve-1` at `10.10.10.11` and `pve-2` at `10.10.10.12`, both placeholders. What I actually run is in [hardware](../docs/hardware.md) and [network](../docs/network.md).

The design is two Proxmox nodes in one cluster with one UI, no shared storage, and no HA. One node is enough to start with. The overlay only matters once guests need to live on more than one box.

The edge firewall has to be up before any of this. Both nodes need Core addresses and DNS at `10.10.10.1`.

## Install each node

1. On the admin PC, download the current Proxmox VE ISO and verify the SHA256.
2. Write it to USB with Rufus in DD mode and boot the machine from it.
3. Target the internal disk and pick a filesystem that fits it. ZFS on a single disk gives you snapshots and checksums but not a mirror. ext4 with LVM thin is simpler on a smaller drive and leaves more RAM for guests. I run one of each, and either works.
4. Set country, timezone, and keyboard.
5. Set a strong root password. The email field is where Proxmox sends alerts.
6. For the network, pick the NIC that reaches Core and set:
   - Hostname `pve-1.home.example.com`, using your Core domain
   - IP `10.10.10.11/24`, or `.12` on the second node
   - Gateway `10.10.10.1`
   - DNS `10.10.10.1`
7. Finish, reboot, and pull the USB.

In the BIOS, enable virtualization (VT-x or AMD-V) and turn Secure Boot off if the installer complains about it.

## First boot

SSH in from the admin PC with `ssh root@10.10.10.11`, then work through the following.

1. Fix the repositories. Disable `pve-enterprise` and enable the no subscription repo. In the UI this is under the node at **Updates → Repositories**. Skip the Ceph repos unless you actually run Ceph.
2. Run `apt update` and `apt dist-upgrade`, then reboot.
3. Set the same timezone on every node and point NTP at `10.10.10.1`. Both the cluster and Active Directory care about the clock.
4. Confirm `/etc/network/interfaces` has a single bridge:

```text
auto vmbr0
iface vmbr0 inet static
    address 10.10.10.11/24
    gateway 10.10.10.1
    bridge-ports enp1s0
    bridge-stp off
    bridge-fd 0
```

The interface name may differ, so check with `ip link`. Don't make `vmbr0` VLAN aware unless the switch is doing VLANs. Tagged frames go nowhere on an unmanaged switch.

5. Add each node's MAC to its reservation on the edge.

Open `https://10.10.10.11:8006` from the admin PC to confirm the UI loads, then repeat all of this on the second node.

![Summary page for my primary node](../images/proxmox/02-polaris-summary.jpg)

If you have two nodes, decide now which one will hold the attack box, and keep identity off it. The domain controller, the SIEM, and the lab router all go on the other node.

## Cluster

Pick any cluster name. Create it on `pve-1` and join from `pve-2`. Corosync rides `vmbr0`.

On `pve-1`:

```bash
pvecm create homelab
```

On `pve-2`:

```bash
pvecm add 10.10.10.11
```

Check `pvecm status` on either node. Both nodes should now show in one UI at `https://10.10.10.11:8006`.

![My cluster tree](../images/proxmox/01-cluster-tree.jpg)

I don't run HA, and with two nodes I couldn't anyway. There's no tiebreaker vote, so if one node dies the survivor can lose quorum and refuse changes. When that happens, tell the live node to expect only itself:

```bash
pvecm expected 1
```

Storage definitions replicate across the cluster, and that trips people up when the nodes don't match. If one node uses ZFS (`rpool` and `local-zfs`) and the other doesn't, pin that store to the ZFS node. After the join, the other node lists it as inactive with `cannot import 'rpool'`. Go to **Datacenter → Storage**, edit that store, and restrict **Nodes** to the one that actually has the pool.

If the second node uses LVM thin and **Datacenter → Storage** has no `local-lvm` entry for it, add one with **Add → LVM-Thin**: ID `local-lvm`, that node only, volume group `pve`, thin pool `data`. Confirm with `pvesm status` and `lvs`. Never create a guest on a store its node can't import.

ISOs are per node too. Upload them to `local` on the node that will run the guest, because the other node's `local` isn't visible there.

![My storage page](../images/proxmox/06-storage.jpg)

## Datacenter firewall

This is **Datacenter → Firewall**, with its Options, IPSet, and Rules tabs. It's not the **SDN → VNet Firewall** page, which I leave empty.

Order matters here. Create the IPSets and rules before you enable anything, or the UI session drops the moment you turn it on.

1. Create two IPSets: `admin` containing the admin PC, and `pve_peers` containing both node addresses.
2. Add rules with direction IN and action ACCEPT:
   - Source `+admin`, TCP, destination ports 8006 and 22
   - Source `+pve_peers`, UDP 5404 to 5405, for corosync
   - Source `+pve_peers`, UDP 4789, for VXLAN
   - Source `+pve_peers`, TCP 8006, 22, 3128, 5900 to 5999, and 60000 to 60050, for the UI proxy, SSH, spice, console, and migration
3. Set the input policy to DROP and the output policy to ACCEPT.
4. Enable the firewall at the datacenter level, then on each node.
5. Confirm the UI still loads from the admin PC and that `pvecm status` still shows both votes. If corosync drops, the peer rule is wrong. Fix it before you go further.

After this, a phone on Core should not be able to open `https://10.10.10.11:8006`.

## SDN overlay

If you only have one node, skip this and put guests on a plain Linux bridge. With two nodes, don't create extra bridges with no NIC attached. A bridge like that only exists on the node where you made it, and guests on the other node can't see it.

Under **Datacenter → SDN**:

1. In Zones, add a VXLAN zone with ID `lab`, both node addresses as peers, and MTU 1450.
2. In VNets, add three in zone `lab`, with no subnets defined on the Proxmox side since `lab-gw` owns the gateways:
   - `labsrv`
   - `labep`
   - `labatk`
3. Apply, then check that both nodes list the vnets under their network views. The `vxlan_*` interfaces show operstate `UNKNOWN` with `UP,LOWER_UP`, and that's normal. The `localnet` zone Proxmox creates can stay, but don't put guests on it.

![My SDN zone](../images/proxmox/04-sdn-zone.jpg)

![My SDN vnets](../images/proxmox/05-sdn-vnets.jpg)

I leave **Datacenter → SDN → VNet Firewall** empty on purpose. Isolation is the lab router VM's job, not a Proxmox VNet rule.

![VNet firewall, empty](../images/proxmox/07-sdn-vnet-firewall.jpg)

Guests on the overlay need MTU 1450. Set it in the guest NIC config under Advanced, or let Proxmox propagate it.

## Lab router VM

Create this on the node that holds identity, not the attack node. OPNsense configuration is in [firewall](firewall.md). This section only covers the VM itself.

- Name `lab-gw`
- OS: the OPNsense **dvd** ISO for amd64, same major version as the edge, uploaded to this node's `local`. Not the vga USB image.
- Machine q35 with SeaBIOS
- Disk on virtio SCSI
- 4 vCPU, type host
- 4 GB RAM with ballooning off. I started at 2 GB and the guest sat at 99% memory, so don't go lower.
- net0 virtio on `vmbr0`
- net1 virtio on `labsrv`
- net2 virtio on `labep`
- net3 virtio on `labatk`
- QEMU guest agent on
- Start at boot on, start order 1
- Options → Firewall **No**

Install OPNsense in the VM the same way you did on the edge, GPT and UFS. Then go back to [firewall](firewall.md) for interfaces, `reply-to`, aliases, and rules.

## How I run it day to day

I work from the admin PC at `https://10.10.10.11:8006`. Every guest gets the QEMU agent, a virtio SCSI disk rather than VirtIO Block or IDE, CPU type host, and MTU 1450 on lab NICs. Windows guests use OVMF and the lab router uses SeaBIOS. Guest firewalls stay off unless I add rules, and the repositories stay on the no subscription list.

## If something breaks

Creating a VM fails with `cannot import 'rpool'`. The ZFS store is visible on a node that doesn't have that pool. Pin it to the right node.

The cluster refuses changes after one host goes down. Two nodes have no tiebreaker. Run `pvecm expected 1` on the live node.

The UI dropped the instant you enabled the firewall. The admin IPSet was empty or the rules weren't saved first. Get in through the console, fix the rules, and try again.

Corosync died after the firewall came on. The peer rule for UDP 5404 to 5405 is missing or the `pve_peers` IPSet is wrong.

Guests on different nodes can't ping each other. You made a local bridge instead of using the VXLAN zone, or the guest MTU is still 1500.

Tagged frames go nowhere. `vmbr0` is VLAN aware and the switch isn't.
