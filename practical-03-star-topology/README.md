# Practical 3 — Star Topology using Hub and Switch

## Aim
To create and simulate a **Star topology** in Cisco Packet Tracer using (a) a **Hub** as the central device and (b) a **Switch** as the central device, and to compare the behaviour of the two.

## Theory

### Star Topology
In a star topology, every end device is connected to a single **central device** (hub or switch) by a dedicated point-to-point link. All communication between end devices passes through the centre.

```
        PC0
         |
  PC3 — HUB/SWITCH — PC1
         |
        PC2
```

**Advantages:**
- Easy to install and wire.
- Failure of one cable affects only one PC; the rest of the network keeps working.
- Easy to detect faults and to add/remove devices.

**Disadvantages:**
- If the central device fails, the entire network goes down.
- Requires more cable than bus/ring.

### Hub vs. Switch — the key difference

| Feature | Hub | Switch |
|---------|-----|--------|
| OSI Layer | Physical (Layer 1) | Data Link (Layer 2) |
| Forwarding | Broadcasts every frame to **all** ports | Forwards to the **specific destination** port based on MAC address |
| Collision Domain | **Single** (all ports share) | **One per port** |
| Bandwidth | Shared among all ports | Dedicated per port |
| Intelligence | None | Learns MAC addresses |

This difference is clearly visible in Packet Tracer's **Simulation Mode**: a hub floods the ping packet to every connected PC, while a switch sends it only to the intended destination (after the first frame teaches it the MAC table).

## Part A — Star Topology with Hub

### Procedure
1. Open Cisco Packet Tracer.
2. Drag **4 PCs** (PC0–PC3) onto the workspace.
3. From **Hubs**, drag one **Hub-PT** onto the workspace.
4. Use **Copper Straight-Through** cables to connect each PC's `FastEthernet0` to a port on the hub.
5. Configure the PCs with IP addresses in the same subnet:

| Device | IP Address | Subnet Mask |
|--------|------------|-------------|
| PC0 | 192.168.1.1 | 255.255.255.0 |
| PC1 | 192.168.1.2 | 255.255.255.0 |
| PC2 | 192.168.1.3 | 255.255.255.0 |
| PC3 | 192.168.1.4 | 255.255.255.0 |

6. From PC0's Command Prompt, run `ping 192.168.1.2`.
7. Switch to **Simulation Mode** (bottom-right) and observe the packet being flooded out of every port of the hub.
8. Save as `star-hub.pkt`.

## Part B — Star Topology with Switch

### Procedure
1. Repeat the topology but replace the Hub with a **Switch** (e.g. `2960-24TT`).
2. Use straight-through cables from each PC to a `FastEthernet` port on the switch.
3. Use the same IP scheme as above.
4. Run `ping 192.168.1.2` from PC0.
5. In **Simulation Mode**, observe that after the first (ARP broadcast) frame, the switch sends subsequent frames only to the destination port.
6. Save as `star-switch.pkt`.

## Expected Output

Ping succeeds in both cases:

```
PC>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
Reply from 192.168.1.2: bytes=32 time<1ms TTL=128
```

Observations in **Simulation Mode**:
- **Hub:** packet visibly travels to every connected PC, including PC2 and PC3 that were never addressed.
- **Switch:** packet goes only from PC0 → Switch → PC1 (after the initial ARP learning phase).

## Observations

1. Both a hub-based and a switch-based star topology provide connectivity.
2. A hub is a dumb repeater that floods frames, wasting bandwidth and creating a single large collision domain.
3. A switch intelligently forwards frames based on MAC addresses and provides each port with its own collision domain.
4. In real-world networks, hubs are obsolete — switches are universally used.

## Conclusion

The Star topology was successfully implemented in Packet Tracer using both a hub and a switch. By observing the two simulations side-by-side, we see the clear advantage of a switch's targeted forwarding over a hub's blind flooding.

 
