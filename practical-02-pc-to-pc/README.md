# Practical 2 — PC-to-PC Communication

## Aim
To study and perform direct PC-to-PC communication in a network simulator (Cisco Packet Tracer) using a crossover cable, configure IP addresses on both hosts, and verify connectivity using the `ping` command.

## Theory

**PC-to-PC communication** is the simplest form of networking — two computers connected directly, without any intermediate device like a hub or switch. For this to work:

1. Both PCs must have a **Network Interface Card (NIC)**.
2. They must be connected with a **crossover cable** (because both ends are identical "end devices" — the cable must cross the TX/RX pairs so that the transmitter on one side meets the receiver on the other).
3. Both PCs must be configured with IP addresses in the **same subnet** (so that they can communicate directly at the network layer without a router).

### Straight-through vs. Crossover Cable
- **Straight-through:** connects unlike devices (PC ↔ Switch, PC ↔ Router, Switch ↔ Router).
- **Crossover:** connects like devices (PC ↔ PC, Switch ↔ Switch, Router ↔ Router).

Modern NICs with Auto-MDIX can auto-negotiate, but in Packet Tracer the distinction is enforced.

### IP Addressing used
| Device | IP Address | Subnet Mask |
|--------|------------|-------------|
| PC0 | 192.168.1.1 | 255.255.255.0 |
| PC1 | 192.168.1.2 | 255.255.255.0 |

Both are in the same `/24` subnet — no gateway is required.

## Procedure

1. Open **Cisco Packet Tracer**.
2. From the bottom-left **End Devices** panel, drag **two PCs** (PC0 and PC1) onto the workspace.
3. From the **Connections** panel (lightning-bolt icon), select the **Copper Cross-Over** cable (the dashed line).
4. Click **PC0** → select `FastEthernet0` → click **PC1** → select `FastEthernet0`. A green link should appear.
5. Configure **PC0**:
   - Click PC0 → go to the **Desktop** tab → click **IP Configuration**.
   - Set IPv4 Address: `192.168.1.1`
   - Set Subnet Mask: `255.255.255.0`
6. Configure **PC1** similarly:
   - IPv4 Address: `192.168.1.2`
   - Subnet Mask: `255.255.255.0`
7. Test connectivity: on PC0, go to **Desktop → Command Prompt** and run:
   ```
   ping 192.168.1.2
   ```
8. Do the reverse test from PC1:
   ```
   ping 192.168.1.1
   ```
9. Save the Packet Tracer file as `pc-to-pc.pkt` in this folder.

## Expected Output

```
PC>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

*(The first reply may time out because of the initial ARP request — this is normal.)*

## Observations

- Two PCs connected by a crossover cable can communicate without any switch or router, provided they are on the same subnet.
- The first ping packet is often lost due to ARP resolution; subsequent packets succeed.
- If different subnets are used, the ping fails — confirming the need for a router in an inter-network.

## Conclusion

PC-to-PC communication was successfully established in Cisco Packet Tracer using a crossover cable and same-subnet IP addressing. Bidirectional `ping` verified end-to-end connectivity.
 
