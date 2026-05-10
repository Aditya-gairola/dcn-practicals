# Practical 8 — Connection Between Devices Using a Router

## Aim
To implement direct connections between two PCs through a router in Cisco Packet Tracer — without using any switch — and to verify reachability between the PCs and the router using the `ping` command.

## Theory

A **router** is a Layer-3 (Network layer) device that forwards packets between IP networks. Unlike a switch, every router interface is in its **own** IP subnet. When two end devices are connected directly to two separate router interfaces, each device sits in a tiny "subnet of two" — itself and the router.

For two PCs to talk to each other through such a router:
1. Each router interface needs an **IP address** in its own subnet, and must be brought up with `no shutdown`.
2. Each PC's IP address must be in the same subnet as the router interface it is wired to.
3. Each PC's **default gateway** must be set to the router interface that it is connected to.

Because the two subnets are *directly connected* to the router, the router automatically knows how to reach both, and inter-subnet packets are forwarded without any additional routing configuration.

### Network used in this practical

- **Router0** — the central router with two FastEthernet/Gigabit interfaces
- **PC0** — connected directly to one router interface (Subnet A)
- **PC1** — connected directly to another router interface (Subnet B)
- The cables between PC and router are **copper crossover** cables (because both PC and router are end-line / "non-hub" devices in this direct connection).

### Topology

```
       PC0  ── (crossover) ──  Router0  ── (crossover) ──  PC1
   192.168.1.1                 Fa0/0           Fa0/1                  192.168.2.1
                            192.168.1.10    192.168.2.10
```

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| PC0 | FastEthernet0 | 192.168.1.1 | 255.255.255.0 | 192.168.1.10 |
| PC1 | FastEthernet0 | 192.168.2.1 | 255.255.255.0 | 192.168.2.10 |
| Router0 | FastEthernet0/0 | 192.168.1.10 | 255.255.255.0 | — |
| Router0 | FastEthernet0/1 | 192.168.2.10 | 255.255.255.0 | — |

## Procedure

1. Open Cisco Packet Tracer.
2. Drag onto the workspace:
   - **1 Router** (`1841` or `2811`) → Router0
   - **2 PCs** (`PC-PT`) → PC0, PC1
3. Wire the devices using **copper crossover** cables (the dashed line):
   - PC0 `FastEthernet0` → Router0 `FastEthernet0/0`
   - PC1 `FastEthernet0` → Router0 `FastEthernet0/1`
4. Configure PC0 and PC1 with the IP, mask, and default gateway shown in the table above (**Desktop → IP Configuration**).
5. Configure the router interfaces using its **CLI**:

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R1

R1(config)# interface FastEthernet0/0
R1(config-if)# ip address 192.168.1.10 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface FastEthernet0/1
R1(config-if)# ip address 192.168.2.10 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# end
R1# copy running-config startup-config
```

6. Verify reachability:

```
PC0> ping 192.168.1.10        ! PC0 → Router (Fa0/0)   ✔
PC0> ping 192.168.2.1         ! PC0 → PC1              ✔
PC1> ping 192.168.2.10        ! PC1 → Router (Fa0/1)   ✔
PC1> ping 192.168.1.1         ! PC1 → PC0              ✔
```

## Expected Output

```
PC>ping 192.168.2.1

Pinging 192.168.2.1 with 32 bytes of data:

Request timed out.
Reply from 192.168.2.1: bytes=32 time<1ms TTL=127
Reply from 192.168.2.1: bytes=32 time<1ms TTL=127
Reply from 192.168.2.1: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.2.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
```

In the Packet Tracer **scenario panel** all four ICMP tests (PC0↔Router, PC1↔Router, PC0↔PC1, PC1↔PC0) show the **Successful** status.

## Observations

1. Two PCs connected through a router (with no switch in between) can communicate provided each PC's default gateway is correctly set to its router interface.
2. The TTL of the reply drops by 1 (`128 → 127`), confirming the packet crossed exactly one router.
3. Even though there are only two PCs, two **separate** subnets (`192.168.1.0/24` and `192.168.2.0/24`) are used because every router interface has to be in its own subnet.
4. The router does not need any static or dynamic routing — both subnets are *directly connected*, so they appear in `show ip route` automatically as `C` (connected) entries.

## Conclusion

A simple router-based interconnection between two PCs was successfully implemented in Cisco Packet Tracer. The router, with one IP-configured interface per PC, forwarded packets between the two subnets, and bidirectional `ping` verified end-to-end connectivity through the router.
