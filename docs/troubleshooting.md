# Troubleshooting

## Common Issues

### VM cannot reach another VM
**Symptoms**
- Ping fails
- No RDP/SSH connection
- Services unreachable

**Checks**
- Verify VirtualBox adapter mode
- Confirm IP addresses and subnet masks
- Check Windows Firewall / UFW rules
- Test with ping, traceroute, and port checks

**Resolution**
TBD

---

### Logs not appearing in SIEM
**Symptoms**
- No events from endpoint
- Forwarder appears installed
- Queries return no data

**Checks**
- Confirm forwarder/agent status
- Validate input configuration
- Check firewall rules
- Generate a test event

**Resolution**
TBD
