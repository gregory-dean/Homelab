# Command center

Sol is my Windows 11 desktop: Ryzen 7 5800X, 32 GB, RTX 3080 Ti, more than 3 TB of NVMe. The git clone for this repo lives here. I do not run the lab hypervisors on it.

## Address

Leave Sol on DHCP. Do not set a manual address in Windows.

1. On Sirius, add a DHCPv4 static map for Sol's Ethernet MAC to `<core>.154`. Put the reservation in the pool. Dnsmasq wants colons, not Windows dashes.
2. Gateway and DNS come from DHCP: `<core>.1`.
3. Confirm `ipconfig` shows `<core>.154`. Renew with `ipconfig /renew` if Windows still has an old ISP address.

Sol does not answer ping. Windows Firewall drops inbound ICMP. Leave it. Prove Sol is up from this desktop with the admin UIs and SSH, not with `ping <core>.154`.

## SSH keys

From PowerShell as your user:

```powershell
ssh-keygen -t ed25519 -C "sol"
```

Accept the default path under your profile `.ssh` folder. If that file already exists, do not overwrite it. Reuse it.

Copy the public key to Polaris and Vega. `scp` is more reliable here than piping through `ssh` in some PowerShell hosts:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh root@<core>.11 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh root@<core>.12 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

Then on both nodes, in `/etc/ssh/sshd_config`: `PasswordAuthentication no`. Reload ssh. Keep a console session open until key login works.

Add the same public key to Sirius (**System → Access → Users → root → Authorized keys**) and to gw-01, then disable password login there too. Test `ssh root@<core>.1` before turning passwords off.

## Browser bookmarks

- Sirius: `https://<core>.1`
- gw-01: `https://<core>.3`
- Proxmox cluster: `https://<core>.11:8006`
- Lyra: `http://<core>.2`
- Wazuh: `https://192.0.2.50`

Accept the self signed certs once. Do not turn off certificate warnings globally.

## Proxmox user

In **Datacenter → Permissions → Users**: add a PAM account for daily use. Grant Administrator on `/`. Enable TOTP. Use this account from the browser. Keep `root@pam` for recovery only.

## ISO library

Create a folder on Sol for ISOs. I use `C:\lab-iso`. Download:

- Current OPNsense amd64 (vga image for Sirius USB, dvd ISO for gw-01)
- Current Proxmox VE ISO
- Windows Server 2022
- Windows 11 Pro
- Ubuntu Server LTS
- Kali Linux installer ISO
- VirtIO win drivers ISO

Verify SHA256 against the vendor page before you write a USB.

Upload ISOs into Polaris `local` for Polaris guests and Vega `local` for Vega guests. I do not NFS that folder. If this desktop sleeps, an install would die.

## What I open from here

SSH is key only to Polaris, Vega, Sirius, and gw-01. The Proxmox web user is a PAM account with TOTP.

Sol can enter the lab. Phones on Lyra cannot. That split is a gw-01 WAN rule plus the Proxmox datacenter firewall, which only accepts 8006 and 22 from `<core>.154` and from the peer node.

## Checks

- `ssh root@<core>.11` works with the key, no password prompt
- Same for Vega, Sirius, and gw-01
- All five admin URLs open from Sol
- A phone on WiFi cannot open `https://<core>.11:8006` after the Proxmox firewall is on
