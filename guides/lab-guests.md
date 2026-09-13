# Lab guests

Addresses on this page use `10.30.10.0/24` for servers, `10.30.20.0/24` for endpoints, and `10.30.30.0/24` for the attack box. Hostnames like `dc-01` are placeholders. What I actually run is in [network](../docs/network.md).

I build every guest from an ISO rather than importing old disks. It's slower the first time and much easier to reason about afterward.

`lab-gw` should already be running from [hypervisor](hypervisor.md) and [firewall](firewall.md). This page is the rest of the range.

## Defaults for every guest

Unless a section below says otherwise, every guest gets:

- Machine type q35
- A virtio SCSI disk (`scsi0`), not VirtIO Block (`virtio0`) and not IDE
- A virtio NIC on the right SDN vnet with MTU 1450, set under Advanced
- The QEMU guest agent on
- CPU type **host** rather than `x86-64-v2-AES`
- Ballooning off on Windows guests and on the lab router
- Options → Firewall **No**
- "Start after created" unchecked on Windows guests, so you can attach the virtio driver CD first

Upload each ISO to `local` on the node that will run the guest, and put the disk on a store that node can actually import.

If you have two nodes, keep the domain controller off the node that holds the attack box.

On Windows guests, set IPv4 in `ncpa.cpl` rather than the Settings app, and allow ICMPv4 echo through Windows Firewall. Otherwise a ping from the admin PC fails even though the guest can reach the DC without any trouble.

## Order

1. Domain controller
2. Linux server, optional and off the domain
3. Domain workstation
4. SIEM
5. Attack box

## Domain controller

- NIC on `labsrv`
- `10.30.10.10/24`, gateway `10.30.10.1`
- DNS `10.30.10.1` during install, then itself after AD DS is up
- Windows Server on OVMF, with a TPM if the ISO asks for one

Start this one early so everything else can find it.

Attach the VirtIO driver ISO for storage (`vioscsi`) and networking (`NetKVM`). During Setup, load the SCSI driver from `vioscsi` or the disk never shows up. After Setup, run `virtio-win-gt-x64.msi` and the guest agent MSI.

Set the hostname, set the address in `ncpa.cpl`, and answer Yes to network discovery.

Promote it to a domain controller for a dedicated lab zone, for example `lab.example.com`. Pick the highest forest functional level in the list. When the DNS delegation warning appears, continue with the checkbox off. And pay attention to the DSRM password prompt: that's a separate recovery password, not the domain Administrator login. Don't reuse the same one for both.

After promotion:

1. On the DC, set DNS to itself and add a forwarder to `10.30.10.1`.
2. On the edge, add Unbound Query Forwarding for the AD zone → the DC.
3. Do the same on the lab router's Unbound.
4. On the lab router WAN, add the pass from the edge to the DC on TCP/UDP 53, above the Core deny. Without it, `nslookup` through `10.10.10.1` times out even though the DC answers on its own address.
5. On the lab router, under **DHCP options**, click **+** and set `dns-server[6]` to the DC on LABSRV and LABEP. Leave LABATK alone.
6. Allow ICMPv4 echo on the DC so the admin PC can ping it.

## Linux server

- NIC on `labsrv`
- `10.30.10.40/24`, gateway `10.30.10.1`, DNS the DC and `10.30.10.1`
- Ubuntu Server LTS

Create a local user during the install. I keep this box off the domain so there's at least one Linux host that doesn't depend on AD.

## Domain workstation

- NIC on `labep`
- `10.30.20.20/24`, gateway `10.30.20.1`, DNS the DC
- Windows 11 Pro on OVMF with TPM 2.0. Home can't join a domain.
- Disk on `scsi0`. If you created it as VirtIO Block, change it before Setup.
- The virtio driver CD on `local`, not on a ZFS ISO store that lives on the other node

Windows 11 OOBE tries hard to force a Microsoft account. Use **Domain join instead** or `oobe\bypassnro` to create a local account first, and don't join the domain during OOBE.

The NIC driver on the virtio CD is under `NetKVM\w11\amd64`, not at the CD root. Allow ICMPv4 echo once you're in.

Join the lab domain as the domain Administrator. The DSRM password won't work here, which catches a lot of people the first time. If the join fails, check `nltest /dsgetdc` and ports 88, 389, and 445 before you start second guessing the password.

## SIEM

- NIC on `labsrv`
- `10.30.10.50/24`, gateway `10.30.10.1`, DNS the DC and `10.30.10.1`
- Ubuntu Server LTS
- More CPU and RAM than the other Linux guests

Install the Wazuh indexer, server, and dashboard once the OS is up. I used the current all in one install from the Wazuh documentation. Bookmark the dashboard on the admin PC and accept the self signed certificate for this host only.

Agents and detections aren't covered here. This page gets the box installed and reachable.

## Attack box

- NIC on `labatk`
- `10.30.30.30/24`, gateway `10.30.30.1`, DNS `10.30.30.1`
- Kali Linux from the **installer** ISO, not a live image
- Off the domain

Confirm it can ping the DC. A failed ping to the admin PC doesn't prove the `lab-gw` deny is working, since the admin PC drops ICMP from every host anyway.

## Checks

From the admin PC:

```powershell
ping 10.30.10.10
ping 10.30.20.20
ping 10.30.10.40
ping 10.30.10.50
ping 10.30.30.30
nslookup dc-01.lab.example.com 10.10.10.1
```

The `nslookup` should return the DC's address, and you should be able to RDP to the workstation as a domain user.

From the attack box, a ping to the DC works. Don't use a ping to the admin PC as your test of the Core deny.

From a phone on WiFi, a ping to the DC fails.

## If something breaks

Windows Setup never sees a disk. The OS disk is VirtIO Block (`virtio0`). Use SCSI and load `vioscsi`.

Windows 11 OOBE demands a Microsoft account. Create a local account first and join the domain later.

The domain join rejects the password you just set. That was the DSRM password. Use the domain Administrator's.

`nslookup` through the edge times out. The lab router WAN is missing the pass from the edge to the DC on port 53.

The admin PC can't ping a Windows guest. The guest is dropping ICMPv4 echo. It can still reach the DC, and it still works.

Creating a VM fails with `cannot import 'rpool'`. The disk is on a store that node can't see. See [hypervisor](hypervisor.md).
