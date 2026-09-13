# Guides

These guides walk through building a lab like mine on whatever hardware you have. The hostnames and addresses are examples, so swap in your own. If you want to see exactly what I run, that's in the [docs](../docs/README.md).

Read them in this order the first time through.

- [Lab layout](layout.md): the shape of the network and why it needs two firewalls
- [Firewall](firewall.md): the edge OPNsense install and the lab router that sits behind it
- [Hypervisor](hypervisor.md): two Proxmox nodes, the cluster, the VXLAN overlay, and the lab router VM
- [Access point](access-point.md): house WiFi as a plain client on the LAN
- [Command center](command-center.md): the PC you administer everything from
- [Lab guests](lab-guests.md): domain controller, workstation, SIEM, Linux server, and attack box
