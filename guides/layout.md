# Lab layout

Across these guides I use a Core LAN of `10.10.10.0/24` and three lab prefixes: `10.30.10.0/24`, `10.30.20.0/24`, and `10.30.30.0/24`. The hostnames are placeholders too, so use whatever fits your setup. If you want to see what I actually run, the [architecture](../docs/architecture.md), [network](../docs/network.md), and [hardware](../docs/hardware.md) pages have it.

This page covers the shape of the lab. The software installs are in the guides that follow.

## What you need

You need an edge firewall running on real hardware, with WAN from the ISP modem and LAN into a switch. Any switch will do. A managed one with VLANs works, and an unmanaged one works too as long as you isolate the lab somewhere else, which is what this layout does. Beyond that you need one or two hypervisors, a PC you can administer from, and optionally an access point for house WiFi.

I use OPNsense for the firewalls and Proxmox for the hypervisors. The roles matter more than the products, so if you prefer something else the layout still holds.

## Why two firewalls

An unmanaged switch is a single Layer 2 network. I call mine Core. The edge firewall only sees traffic that leaves that network, so two devices on the same switch can talk to each other without the firewall ever knowing. That means the edge can't isolate a cyber range from the phones and laptops sitting next to it.

So I run a second OPNsense install as a VM and call it `lab-gw`. It has one leg on Core and owns the lab networks, and every lab packet has to cross it to go anywhere. It doesn't NAT. The edge still NATs everything out the WAN, and it just routes lab traffic to `lab-gw`. Isolation lives on the VM.

If your switch does VLANs you trust, you could put the lab on tagged ports instead. Mine doesn't, so the lab is a VXLAN overlay between the hypervisors.

## Overlay, not extra bridges

A Linux bridge with no physical NIC attached only exists on the node where you created it. Guests on two different hypervisors would never share a Layer 2 segment through it.

VXLAN between the nodes (UDP 4789, MTU 1450 in this example) is what lets an attack box on one host reach a domain controller on the other. The lab router VM gets four NICs: one on Core and one on each of the three vnets.

## Access point

The AP is just another client on Core. It shouldn't run DHCP and it shouldn't NAT. A guest SSID doesn't buy you anything on a flat switch either, because that traffic still lands on Core. Phones can reach the internet and the admin PC, but they shouldn't get into the lab, and that deny is a WAN rule on `lab-gw` rather than anything on the AP.

## Build order

1. Bring up the edge firewall so the house has internet and Core has DHCP and DNS. [Firewall](firewall.md)
2. Install the hypervisors, cluster them, build the overlay, and create the lab router VM. [Hypervisor](hypervisor.md)
3. Finish the `lab-gw` rules. Back to [firewall](firewall.md).
4. Put the AP in access point mode. [Access point](access-point.md)
5. Set up keys and bookmarks on the admin PC. [Command center](command-center.md)
6. Build the lab guests. [Lab guests](lab-guests.md)
