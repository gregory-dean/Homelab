# Switching

The LS108GP is a plug and play PoE+ switch. Eight gigabit ports, 62 W budget, no web UI, no VLANs.

I treat it as one Core LAN. Isolation happens in Proxmox and on gw-01, not here.

Do this after Sirius, Polaris, Vega, and gw-01 already have addresses. Recabling will interrupt each host for a few seconds. It should not change an address. A wrong port on Sirius drops the house. A wrong port on Polaris drops gw-01 and the `10.30` routes.

## Buttons

- Extend mode off. Ports 1 and 2 stay full gigabit. On this model Extend only hits those two ports, and on they drop to 10 Mbps for long camera runs. Port 1 is Sirius. Port 2 is Polaris. I want gigabit.
- PoE Auto Recovery off. On, the switch power-cycles a quiet PoE device. Nothing here draws PoE.

Lyra has its own power brick. The 62 W budget stays unused. Per-port PoE LEDs should stay off.

There is no management address. Do not scan for `192.168.0.1` or a TP-Link cloud app.

## Port map

Rear of the panel is permanent. Front cords are 0.5 ft Cat6A into the matching switch port. Device runs are 2 ft Cat6A.

| Panel | Switch | Device | What you plug |
| ----- | ------ | ------ | ------------- |
| 1 | 1 | Sirius LAN | I350 **port 2** only |
| 2 | 2 | Polaris | Onboard RJ45 |
| 3 | 3 | Vega | Onboard RJ45 |
| 4 | 4 | Sol | Desktop Ethernet |
| 5 | 5 | Lyra | A **LAN** port, not WAN, and only after AP mode |
| 6 to 12 | 6 to 8 | open | Empty. No keystone required. |

Cables that never touch this panel:

- Sirius I350 port 1 to the ISP modem
- Sirius I350 ports 3 and 4 (unused)
- Sirius onboard NIC (unused)

Write the five labels before they go on the hardware. Same text on the panel face and the switch face.

- `1 SIRIUS LAN`
- `2 POLARIS`
- `3 VEGA`
- `4 SOL`
- `5 LYRA`

No MAC addresses and no WAN address on a label.

## Before you pull a cable

From Sol, prove the current path still works:

```powershell
ping -n 4 <core>.1
ping -n 4 <core>.3
ping -n 4 192.0.2.1
ping -n 4 198.51.100.1
ping -n 4 203.0.113.1
```

Do not ping `<core>.11` or `<core>.12`. The datacenter firewall input policy is DROP. Sol is allowed TCP 8006 and 22 only. Prove those nodes with `https://<core>.11:8006` and SSH. Also open `https://<core>.1` and `https://<core>.3`. If any of that fails now, stop. This guide will not fix a software problem.

Confirm you can tell the Tinys apart from the rear. Sirius is the top Tiny and the only one with the I350 bracket.

## Front patches

Front is disposable. Rear is permanent. If I ever replace the switch, I pull five 0.5 ft cords and the map stays.

1. 0.5 ft Cat6A, panel 1 to switch 1, through panel 5 to switch 5. Straight across. No crossing.
2. Push until both ends click. A half-seated 0.5 ft looks fine and has no link.
3. Switch ports 6, 7, and 8 stay empty.

## Rear runs, one host at a time

Move one device. Watch the matching switch Link/Act LED. Confirm the host. Only then pull the next cable.

I do Vega, then Polaris, then Sol, then Sirius LAN last. Lyra last of all, and only if it is already an AP.

Each rear cable is 2 ft Cat6A from the rear of that keystone to the device NIC. Dress them down the back. Do not crush the jacket under a Tiny tray. If 2 ft will not reach Sol or Lyra, use a longer Cat6A for that run only. Do not change the port number.

### Vega (panel 3 / switch 3)

1. Unplug Vega's current Core cable.
2. Rear of panel 3 to Vega onboard RJ45.
3. Switch port 3 Link/Act on.
4. From Sol: the Proxmox tree still shows Vega, or `ssh root@<core>.12` still connects.

### Polaris (panel 2 / switch 2)

Polaris holds gw-01. This move flaps `<core>.11` and `<core>.3`. Sirius gateway monitoring may mark `GW01` down for a few seconds. That is expected.

1. Unplug Polaris onboard.
2. Rear of panel 2 to Polaris onboard RJ45. Not a hole from the old riser.
3. Switch port 2 Link/Act on. Confirm Extend is still off.
4. From Sol: `ping -n 4 <core>.3`, `ping -n 4 192.0.2.1`, then the Proxmox UI and SSH to `.11`.

If `192.0.2.1` dies and stays dead after the link is up, wait for Sirius **System → Gateways → Status** to bring `GW01` back, or ping `<core>.3` from Sirius **Interfaces → Diagnostics → Ping**. Do not add routes. The routes already exist.

### Sol (panel 4 / switch 4)

You will lose the desktop NIC for a few seconds. Do this at the desk, not over a remote session that rides this cable.

1. Unplug Sol's current Ethernet.
2. Rear of panel 4 to Sol.
3. Switch port 4 Link/Act on.
4. On Sol: `ipconfig` and `ping -n 4 <core>.1`. You still want `<core>.154`, gateway and DNS `<core>.1`. If Windows shows a `169.254` or an old ISP address, `ipconfig /renew`.

Leave inbound ICMP blocked on Sol. Do not use ping *to* `<core>.154` as a check.

### Sirius LAN (panel 1 / switch 1)

Last on purpose. House internet and Core DHCP live here.

1. Confirm the modem is still on I350 **port 1**. Do not touch that cable.
2. Unplug the current LAN cable from I350 **port 2** only.
3. Rear of panel 1 to I350 port 2.
4. Switch port 1 Link/Act on.
5. From Sol: `ping -n 4 <core>.1`, internet still works, `https://<core>.1` opens.

If Sol loses DHCP after this move, you plugged port 1 (WAN) or an unused I350 port. The bracket label is the check.

### Lyra (panel 5 / switch 5)

If Lyra is still a router, land the 2 ft cable on the rear of panel 5 and coil the other end. Do not insert it until [access-point](access-point.md) is done.

If Lyra is already an AP at `<core>.2`:

1. 2 ft from rear of panel 5 to a Lyra LAN port.
2. Switch port 5 Link/Act on.
3. From Sol: `ping -n 4 <core>.2` and open the Lyra UI.
4. A phone on WiFi should show a `<core>.1xx` address, gateway `<core>.1`. If the phone gets `192.168.0.x` or `192.168.1.x`, Lyra is still serving DHCP. Unplug it.

## If a port has no link

1. Reseat both ends of the 0.5 ft, then both ends of the 2 ft. The keystone is a coupler. Either side can sit proud and look seated.
2. Swap in a spare 0.5 ft, then a spare 2 ft, then a spare keystone. One change at a time.
3. Plug the device 2 ft straight into the switch, bypassing the panel. If link returns, the keystone or the front patch is the fault.
4. Vega vs Polaris: confirm you did not swap the middle and bottom Tinys.
5. Sirius: confirm you did not land LAN on I350 port 1.

Do not factory-reset Sirius or the Proxmox nodes to "fix" a dark LED.

## Checks

- Both hardware buttons off
- Per-port PoE LEDs off. PoE MAX off
- Switch ports 1 through 5 Link/Act on (port 5 only if Lyra is plugged)
- Switch ports 6 through 8 dark
- Panel 1 to 5 labeled the same as switch 1 to 5
- Sirius WAN still on I350 port 1
- `Get-NetAdapter` on Sol shows 1 Gbps, not 10 or 100 Mbps
- The same pings and UIs from the baseline still work

Expected non-issues: no switch IP to ping, Sol does not answer ping, Sol cannot ping Polaris or Vega, `vxlan_*` still shows UNKNOWN.
