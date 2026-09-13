# Access point

The management address here is `10.10.10.2` and the hostname is `ap`, both placeholders. Mine is in [network](../docs/network.md).

Any home router that has an access point mode will do. It provides house WiFi and nothing else. It's not a second router, and the moment it starts acting like one you'll know, because a phone will show up with a `192.168.0.x` or `192.168.1.x` address. If that ever happens, unplug the AP and fix DHCP before anything else. The edge has to be the only DHCP server on Core.

## Switch it to access point mode

Do this from the admin PC over Ethernet, not from a phone on WiFi, because the AP drops the wireless side while it reboots.

1. Open the AP's current address. Fresh firmware usually answers at a vendor name like `http://tplinkwifi.net` or at `192.168.0.1`. Once it's on Core, the edge lease table under **Services → Dnsmasq DNS & DHCP** will show it.
2. Change the operation mode to Access Point. If it's still in Router mode, convert it before it lives on Core.
3. Make sure DHCP is off. Access point mode usually removes the page entirely, but if it's still there, turn it off.
4. Turn the guest network off unless you have a real isolated VLAN behind it. A guest SSID on a flat switch is still Core, so it wouldn't keep phones away from the admin PC.
5. Turn off IPTV, extra VLAN features, and anything else you don't specifically need.

## Give it a fixed address on Core

1. On the edge, under **Services → Dnsmasq DNS & DHCP → Hosts**, add a reservation for the AP at `10.10.10.2`. Keep it outside the `100` to `199` pool, and remember Dnsmasq wants colons in the MAC rather than the dashes Windows prints. The Unbound host override should already exist from the [firewall](firewall.md) guide.
2. On the AP, set the LAN IPv4 address to static `10.10.10.2`, mask `255.255.255.0`, gateway `10.10.10.1`, DNS `10.10.10.1`. Save it. It'll probably reboot.
3. When it comes back, `ping -n 4 10.10.10.2` from the admin PC and open `https://10.10.10.2`, or plain HTTP if that firmware only does HTTP.
4. If the UI never comes back but phones still have WiFi, the AP took a different address. Check the edge lease table, then set the static address again.

![The network map on my access point](../images/lyra/01-network-map.jpg)

Change the admin password if it's still the factory default.

## Cable

Run the uplink from one of the AP's LAN ports to the Core switch, and leave the WAN port empty. I do this after the reboot into access point mode, not before.

Use the AP's own power supply unless you're sure the switch and the AP both handle PoE correctly.

## Wireless

Keep the existing SSID names and passwords if people already use them. Nobody in the house should have to rejoin because you rebuilt the network.

There's no guest VLAN on an unmanaged switch, so leave the guest network off.

## Checks

- The AP answers at its Core address from the admin PC
- `nslookup` for the AP name returns that address
- A phone on WiFi gets a `10.10.10.1xx` address with gateway and DNS `10.10.10.1`
- The internet works on WiFi
- That phone cannot open the lab router UI at `https://10.10.10.3`
- A phone never gets a `192.168.x.x` address on this SSID

The AP doesn't NAT and it isn't a firewall. Phones on WiFi can reach the internet and the admin PC. They can't reach the lab, but that's because `lab-gw` denies Core (apart from the admin PC) from entering `10.30.0.0/16`, not because of anything the AP does.

## If something breaks

A phone gets a `192.168.x.x` address. The AP is still in router mode or still serving DHCP. Unplug it and go back to the first section.

There seem to be two DHCP servers. Either the modem is in router mode or the AP is. Core gets exactly one, and it's the edge.

The AP's UI vanished after you set a static address. It took a different address than you typed. Check the edge lease table.

The guest SSID doesn't isolate anything. On a flat switch that traffic is still Core. Turn it off.
