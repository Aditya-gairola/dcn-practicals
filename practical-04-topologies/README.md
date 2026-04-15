# Practical 4 — Bus, Ring, Tree, Hybrid, and Mesh Topologies

## Aim
To design, simulate, and study the following network topologies in Cisco Packet Tracer:
1. **Bus** topology
2. **Ring** topology
3. **Tree** topology
4. **Hybrid** topology
5. **Mesh** topology

## Theory

A **network topology** is the physical or logical arrangement of nodes and links in a network. Different topologies offer different trade-offs in terms of cost, fault-tolerance, and performance.

---

### 1. Bus Topology

All devices share a single common communication line (the "backbone"). Both ends are terminated. Only one device can transmit at a time (CSMA/CD).

```
PC0 ─┬─ PC1 ─┬─ PC2 ─┬─ PC3
     |       |       |
    (shared backbone)
```

- **Advantages:** cheap, easy to install for small networks.
- **Disadvantages:** if the backbone fails, the whole network goes down; performance drops as more devices are added.

> Packet Tracer does not ship with a literal "bus" cable, so a bus topology is simulated by connecting PCs through a **hub** — which functions as a single shared segment (one collision domain), just like a true bus.

---

### 2. Ring Topology

Each device is connected to exactly two others, forming a closed loop. Data travels in one direction (or both, in a dual-ring) using a **token-passing** mechanism.

```
   PC0 ── PC1
    |      |
   PC3 ── PC2
```

- **Advantages:** predictable performance; no collisions.
- **Disadvantages:** failure of any single device or link breaks the ring (unless it's a dual-ring).

> In Packet Tracer, a ring is emulated using switches connected to each other in a loop (with Spanning Tree Protocol preventing actual broadcast storms).

---

### 3. Tree Topology

A hierarchical topology combining characteristics of star and bus. A **root** switch/hub branches out to secondary switches, each with its own cluster of end devices.

```
          Root-Switch
           /       \
      Switch1     Switch2
      /    \      /    \
    PC0   PC1   PC2   PC3
```

- **Advantages:** scalable; easy to manage in hierarchical organisations.
- **Disadvantages:** heavy reliance on the root; root failure disconnects large subtrees.

---

### 4. Hybrid Topology

A combination of two or more different topologies — e.g. a star-ring hybrid or a star-bus hybrid. Most real-world enterprise networks are hybrid.

```
Star + Mesh hybrid example:

  [Star cluster 1] ── Core-Switch ── [Star cluster 2]
                      /     \
                  Mesh-link  Mesh-link
                      \     /
                    Redundant-Core
```

- **Advantages:** flexible; combines strengths of constituent topologies.
- **Disadvantages:** complex design and maintenance.

---

### 5. Mesh Topology

Every device is connected to every other device. For *n* devices, `n(n-1)/2` links are required (full mesh).

```
   PC0 ──── PC1
    | \    / |
    |  \  /  |
    |   \/   |
    |   /\   |
    |  /  \  |
    | /    \ |
   PC3 ──── PC2
```

- **Advantages:** extremely fault-tolerant; multiple redundant paths.
- **Disadvantages:** expensive and difficult to wire beyond a handful of devices.

> In Packet Tracer, a mesh is built by connecting each switch to every other switch.

---

## Procedure — General

For every topology:

1. Open a new Packet Tracer file.
2. Drag the required devices (PCs + hub/switches) onto the workspace.
3. Connect them with **copper straight-through** cables (for PC↔hub/switch) or **copper crossover** cables (for hub↔hub, switch↔switch).
4. Configure all PCs with IP addresses in the same subnet: `192.168.1.1/24`, `192.168.1.2/24`, …
5. Verify connectivity by pinging between PCs.
6. Save the file with a descriptive name (e.g. `bus.pkt`, `ring.pkt`, etc.).

### Recommended device counts

| Topology | Devices |
|----------|---------|
| Bus | 1 Hub + 4 PCs |
| Ring | 4 Switches + 4 PCs (one per switch) |
| Tree | 1 Root Switch + 2 Branch Switches + 4 PCs |
| Hybrid | 2 Switches (star clusters) + 1 ring/mesh link |
| Mesh | 4 Switches (fully inter-connected) + 4 PCs |

## IP Addressing (same for all topologies)

| Device | IP Address | Subnet Mask |
|--------|------------|-------------|
| PC0 | 192.168.1.1 | 255.255.255.0 |
| PC1 | 192.168.1.2 | 255.255.255.0 |
| PC2 | 192.168.1.3 | 255.255.255.0 |
| PC3 | 192.168.1.4 | 255.255.255.0 |

## Verification

From **PC0**, ping every other PC:
```
ping 192.168.1.2
ping 192.168.1.3
ping 192.168.1.4
```
All four pings should succeed in each topology.

## Comparison Table

| Feature | Bus | Ring | Star | Tree | Mesh | Hybrid |
|---------|-----|------|------|------|------|--------|
| Cost | Low | Medium | Medium | High | Very High | Variable |
| Installation | Easy | Medium | Easy | Medium | Hard | Hard |
| Fault tolerance | Poor | Poor (single ring) | Good | Medium | Excellent | Good |
| Scalability | Poor | Medium | Good | Excellent | Poor | Good |
| Central point of failure | Backbone | — | Hub/Switch | Root | None | Depends |

## Conclusion

All five topologies were successfully implemented and tested in Cisco Packet Tracer. Each topology has distinct structural, cost, and fault-tolerance characteristics. Real-world enterprise networks almost always use **hybrid** topologies that combine the strengths of star (ease of management) and mesh (redundancy).

## Screenshots to include
- `01-bus-topology.png`
- `02-ring-topology.png`
- `03-tree-topology.png`
- `04-hybrid-topology.png`
- `05-mesh-topology.png`
- `06-ping-results.png`
