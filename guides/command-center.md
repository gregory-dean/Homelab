# Command center

The admin PC here is `admin` at `10.10.10.154`, a placeholder. Mine is in [hardware](../docs/hardware.md).

This is the PC I do everything from: a normal desktop on Core that holds the SSH keys, the browser bookmarks, and the ISO library. It isn't a hypervisor for the lab, and I'd keep it that way. When the lab is broken, you want the machine you're fixing it from to be boring.

## Address

Leave the desktop on DHCP and don't set a manual address in the OS. Instead:

1. On the edge, add a Dnsmasq static map for the desktop's Ethernet MAC to `10.10.10.154`. Putting the reservation inside the pool is fine as long as the client stays on DHCP. Dnsmasq wants colons in the MAC, not the dashes Windows prints.
2. Gateway and DNS come from DHCP as `10.10.10.1`.
3. Confirm the OS shows that address. If it's still holding an old ISP address, renew the lease.

Windows Firewall drops inbound ICMP by default, so a failed ping to the admin PC from another machine doesn't mean anything is wrong. Prove the admin PC is working by opening the admin UIs and SSH from it.

## SSH keys

From PowerShell as your normal user:

```powershell
ssh-keygen -t ed25519 -C "admin"
```

Accept the default path under the `.ssh` folder in your profile. If a key already exists there, reuse it rather than overwriting it.

Copy the public key to each hypervisor:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh root@10.10.10.11 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh root@10.10.10.12 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

Then on both nodes, set `PasswordAuthentication no` in `/etc/ssh/sshd_config` and reload ssh. Keep a console session open until you've confirmed key login works.

Add the same public key to the edge and to `lab-gw`, then disable password login there as well. Test the key against each box before you turn passwords off on it.

## Browser bookmarks

- Edge: `https://10.10.10.1`
- Lab router: `https://10.10.10.3`
- Proxmox cluster: `https://10.10.10.11:8006`
- Access point: `https://10.10.10.2`
- SIEM: `https://10.30.10.50`

Accept each self signed certificate once for that host. Don't turn certificate warnings off globally to make the nagging stop.

## Proxmox user

Under **Datacenter → Permissions → Users**, add a PAM account for daily use, grant it Administrator on `/`, and enable TOTP. Use that account from the browser and keep `root@pam` for recovery only.

## ISO library

Make a folder on the admin PC for ISOs and download:

- The current OPNsense amd64 images, the vga image for the edge USB and the dvd ISO for the lab router VM
- The current Proxmox VE ISO
- Windows Server
- Windows 11 Pro, since Home can't join a domain
- Ubuntu Server LTS
- The Kali Linux installer ISO
- The VirtIO drivers ISO for Windows guests

Verify the SHA256 against the vendor's page before you write anything to USB.

Upload ISOs into `local` on whichever node will run the guest. I don't share this folder over NFS from the desktop, because a desktop sleeps, and an install that's reading from it dies when it does.

## What this PC can reach

SSH to the hypervisors, the edge, and `lab-gw` is key only. The Proxmox web login is the PAM account with TOTP.

The admin PC is the only Core client that can enter the lab. Phones on WiFi can't. That split is a WAN rule on `lab-gw` plus the Proxmox datacenter firewall, which only accepts 8006 and 22 from the admin address and the peer node.

## Checks

- SSH to each hypervisor works with the key and never asks for a password
- The same is true for the edge and `lab-gw`
- All five bookmarks open from this PC
- A phone on WiFi can't open the Proxmox UI once the datacenter firewall is on

## If something breaks

Pinging the admin PC fails. Windows Firewall is dropping inbound ICMP. Use the UIs and SSH to prove it's up.

An install died partway through reading an ISO over NFS. The desktop went to sleep. Keep ISOs on the node that's installing.

Key login fails and you already disabled passwords. Get to the physical console. And do the boxes one at a time so you never lock yourself out of all four at once.
