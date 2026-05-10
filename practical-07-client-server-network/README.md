# Practical 7 — Client–Server Network

## Aim
To design, configure, and verify a simple **Client–Server network** in Cisco Packet Tracer where multiple client machines (PCs and a Laptop) communicate with a central server through a switch.

## Theory

In a **client–server architecture**, one or more powerful machines act as **servers** that provide services (file sharing, web pages, DNS, DHCP, email, etc.), while the rest of the machines act as **clients** that consume those services.

Key characteristics:
- **Centralised** — all data and services reside on the server.
- **Scalable** — clients can be added without affecting other clients.
- **Manageable** — administration, security, and backups happen at one place.
- **Reliable** — if the server fails, all services go down (single point of failure).

This contrasts with **peer-to-peer (P2P)** networks where every node is both a client and a server.

### Network used in this practical

- **Server0** — the central server (file/web/DNS host)
- **Switch0** — Layer-2 switch connecting all devices
- **PC0**, **PC1** — desktop clients
- **Laptop0** — laptop client

All four end devices are connected to a single switch and share the same `192.168.1.0/24` subnet.

### Topology

```
              Server0
                |
              Switch0
            /   |    \
       Laptop0 PC0   PC1
```

## IP Addressing

| Device | IP Address | Subnet Mask |
|--------|------------|-------------|
| Server0 | 192.168.1.100 | 255.255.255.0 |
| PC0 | 192.168.1.1 | 255.255.255.0 |
| PC1 | 192.168.1.2 | 255.255.255.0 |
| Laptop0 | 192.168.1.3 | 255.255.255.0 |

## Procedure

1. Open Cisco Packet Tracer.
2. Drag the following onto the workspace:
   - **1 Server** (`Server-PT`) → Server0
   - **1 Switch** (`2960-24TT`) → Switch0
   - **2 PCs** (`PC-PT`) → PC0, PC1
   - **1 Laptop** (`Laptop-PT`) → Laptop0
3. Wire all four end devices to the switch using **copper straight-through** cables (`FastEthernet0` on the end device → any `FastEthernet0/x` port on the switch).
4. Configure each end device's IP address and subnet mask via **Desktop → IP Configuration**.
5. Optionally enable services on the server (HTTP, DNS, DHCP) under the **Services** tab.
6. From a client (e.g. PC0), open **Desktop → Web Browser** and visit `http://192.168.1.100` to see the default Packet Tracer web page served by Server0.
7. Verify connectivity using `ping`:
   ```
   PC0> ping 192.168.1.100        ! client → server
   Server0> ping 192.168.1.1      ! server → client
   ```

## Expected Output

```
PC>ping 192.168.1.100

Pinging 192.168.1.100 with 32 bytes of data:
Reply from 192.168.1.100: bytes=32 time<1ms TTL=128
Reply from 192.168.1.100: bytes=32 time<1ms TTL=128
Reply from 192.168.1.100: bytes=32 time<1ms TTL=128
Reply from 192.168.1.100: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.100:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
```

The Packet Tracer **scenario panel** at the bottom-right confirms successful ICMP exchanges between every client and the server.

## Observations

1. All clients can reach the server, and the server can reach every client — a textbook many-to-one star.
2. The switch forwards frames only to the destination port (after MAC learning), so server traffic doesn't bother other clients.
3. If the **server** is shut down, every service it offers is lost, but client-to-client connectivity through the switch still works.
4. If the **switch** fails, the entire network stops — confirming the single point of failure inherent to a star/client-server design.

## Conclusion

A client–server network with one server, one switch, two PCs, and one laptop was successfully implemented in Cisco Packet Tracer. Bidirectional connectivity between all clients and the central server was verified using `ping`, demonstrating the basic operation of the centralised model.
