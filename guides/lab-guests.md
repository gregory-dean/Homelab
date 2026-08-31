# Lab guests

I rebuilt these from ISO. I did not import old disks.

`gw-01` is already running from [hypervisor](hypervisor.md) and [firewall](firewall.md). This page is the rest of the range.

## Defaults

Unless a guest says otherwise:

- Machine q35
- Disk: virtio SCSI (`scsi0`). Not VirtIO Block (`virtio0`) and not IDE
- NIC: virtio, on the SDN vnet, MTU 1450 (tick Advanced)
- QEMU guest agent on
- CPU type **host**, not `x86-64-v2-AES`
- Balloon off on Windows and on gw-01
- Options → Firewall **No**
- Create VM: Start after created **off** until the virtio CD is attached on Windows guests

Vega disk store is `local-lvm`. Do not use `local-zfs` on Vega. Upload Ubuntu and Kali ISOs to Vega `local`.

Set Windows IPv4 in `ncpa.cpl`, not Settings. Allow ICMPv4 echo on Windows guests or Sol’s ping check fails even when the guest can ping the DC.

## Order

1. `dc-01`
2. `ubuntu-01`
3. `winclient-01`
4. `siem-01`
5. `kali-01`

## dc-01

- Node: Polaris. VM ID 101. Storage `local-zfs`
- 2 vCPU, 4 GB RAM, 80 GB disk
- NIC on `labsrv`
- IP `192.0.2.10/24`, gateway `192.0.2.1`
- DNS `192.0.2.1` during install, then itself after AD DS
- OS: Windows Server 2022, OVMF, add TPM if the ISO asks
- Start order 2

Attach the VirtIO driver ISO for storage (`vioscsi`) and network (`NetKVM`). During Setup, load the SCSI driver from `vioscsi` or the disk never appears. After Setup, run `virtio-win-gt-x64.msi` plus the guest-agent MSI.

Hostname `dc-01`. Address in `ncpa.cpl`. Discoverable Yes.

Promote to domain controller for `lab.lan`, NetBIOS `LAB`. Forest functional level: highest in the list (2016 is fine on this ISO). DNS delegation warning: continue, checkbox off. DSRM is a new password, not the domain Administrator login. Do not reuse them.

After promotion:

1. On `dc-01`, set DNS to `192.0.2.10` and add a DNS forwarder to `192.0.2.1`.
2. Sirius Unbound Query Forwarding `lab.lan` → `192.0.2.10`.
3. Same forwarding on gw-01 Unbound.
4. gw-01 WAN pass `SIRIUS` → `DC` TCP/UDP 53, above the `CORE_NET` deny. Without it, `nslookup` via `<core>.1` times out while the DC answers on `.10`.
5. gw-01 **DHCP options → +**: `dns-server[6]` = `192.0.2.10` on LABSRV and LABEP. Do not change LABATK.
6. Allow ICMPv4 echo on the guest so Sol can ping `.10`.

## ubuntu-01

- Node: Vega. VM ID 201. Storage **`local-lvm`**
- 2 vCPU, 4 GB RAM, 32 GB disk
- NIC on `labsrv`
- IP `192.0.2.40/24`, gateway `192.0.2.1`, DNS `192.0.2.10` and `192.0.2.1`
- OS: Ubuntu Server LTS
- Hostname `ubuntu-01`
- Stays off domain
- Start order 10

Upload the Ubuntu ISO to Vega `local` first. Create a local user during install. I do not join this box to AD.

## winclient-01

- Node: Polaris. VM ID 102. Storage `local-zfs`
- 2 vCPU, 4 GB RAM, 80 GB disk
- NIC on `labep`
- IP `198.51.100.20/24`, gateway `198.51.100.1`, DNS `192.0.2.10`
- OS: Windows 11 Pro. Home cannot join a domain. OVMF, TPM 2.0
- Disk `scsi0`. Change off VirtIO Block before Setup. Virtio CD on `local`, not `local-zfs`
- Start order 10

Windows 11 OOBE will try to force a Microsoft account. Use **Domain join instead** / `oobe\bypassnro` and create a local account first. Do not join the domain during OOBE.

NIC driver from the virtio CD is `NetKVM\w11\amd64`, not the CD root. Allow ICMPv4 echo.

Join `lab.lan` as `LAB\Administrator` (domain admin, not DSRM). DSRM will not join. If join fails, check `nltest /dsgetdc` and ports 88 / 389 / 445 before you chase the password.

## siem-01

- Node: Polaris. VM ID 103. Storage `local-zfs`
- 4 vCPU, 8 GB RAM, 80 GB disk
- NIC on `labsrv`
- IP `192.0.2.50/24`, gateway `192.0.2.1`, DNS `192.0.2.10` and `192.0.2.1`
- OS: Ubuntu Server LTS
- Hostname `siem-01`
- Start order 3

Install Wazuh indexer, server, and dashboard after the OS is up. I used the current all-in-one install from the Wazuh docs. Bookmark `https://192.0.2.50` on Sol. Accept the self-signed cert for this host only.

Agents and detections are not on this page. The box is installed and reachable.

## kali-01

- Node: Vega. VM ID 202. Storage **`local-lvm`**
- 2 vCPU, 4 GB RAM, 40 GB disk
- NIC on `labatk`
- IP `203.0.113.30/24`, gateway `203.0.113.1`, DNS `203.0.113.1`
- OS: Kali Linux. Use the **installer** ISO, not a live-only image
- Hostname `kali-01`
- Stays off domain
- Start order 10

Upload the ISO to Vega `local`. Confirm it can ping `192.0.2.10`. A failed ping to Sol (`<core>.154`) is not proof of the gw-01 deny. Sol drops ICMP from every host.

## Resource sanity

Polaris used: gw-01 4 GB, dc-01 4 GB, winclient-01 4 GB, siem-01 8 GB. About 20 GB of 32 GB, plus the host.

Vega used: ubuntu-01 4 GB, kali-01 4 GB. About 8 GB of 32 GB, plus the host.

Disk: Polaris about 272 GB of 512 GB before host overhead. Vega about 72 GB of 250 GB.

## Checks

From Sol:

```powershell
ping 192.0.2.10
ping 198.51.100.20
ping 192.0.2.40
ping 192.0.2.50
ping 203.0.113.30
nslookup dc-01.lab.lan <core>.1
```

`nslookup` should return `192.0.2.10`. RDP to winclient-01 as a domain user.

From kali-01: ping dc-01 works. Do not use ping to Sol as the Core deny check.

From a phone on Lyra: ping dc-01 fails.
