# Practical 6 — Initial Router Configuration

## Aim
To perform an initial configuration of a Cisco router in Packet Tracer — connecting two different IP subnets (two LANs) through a single router so that devices on different subnets can communicate with each other.

## Theory

A **router** is a Layer-3 (Network layer) device that forwards packets between different IP networks. Unlike a switch, which only forwards frames inside a single broadcast domain, a router has interfaces in **separate subnets** and uses its **routing table** to decide where each packet should go.

Each router interface must be:
1. Assigned an **IP address** inside the subnet it connects to.
2. Brought up with `no shutdown` (Cisco router interfaces are administratively down by default).

Every host in a given subnet must have its **default gateway** set to the router interface that serves that subnet. Without a default gateway, a host does not know where to send packets whose destination lies outside its own subnet.

### Network Used in this Practical

- 4 PCs, 2 Switches, 1 Router
- **Subnet A** (10.10.10.0/24) — PC0 and PC1 connected through Switch0
- **Subnet B** (20.20.20.0/24) — PC2 and PC3 connected through Switch1
- The **router** has one interface in each subnet and forwards packets between them

### Topology

```
                       Router0
                    /          \
             G0/0 (10.10.10.10)  G0/1 (20.20.20.10)
                  |                   |
               Switch0             Switch1
               /    \              /    \
             PC0    PC1          PC2    PC3
         10.10.10.1 10.10.10.2  20.20.20.1 20.20.20.2
```

## IP Addressing Plan

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| PC0 | FastEthernet0 | 10.10.10.1 | 255.255.255.0 | 10.10.10.10 |
| PC1 | FastEthernet0 | 10.10.10.2 | 255.255.255.0 | 10.10.10.10 |
| PC2 | FastEthernet0 | 20.20.20.1 | 255.255.255.0 | 20.20.20.10 |
| PC3 | FastEthernet0 | 20.20.20.2 | 255.255.255.0 | 20.20.20.10 |
| Router0 | GigabitEthernet0/0 | 10.10.10.10 | 255.255.255.0 | — |
| Router0 | GigabitEthernet0/1 | 20.20.20.10 | 255.255.255.0 | — |

## Procedure

1. Open Cisco Packet Tracer.
2. Drag **4 PCs (PC0–PC3)**, **2 Switches** (`2960-24TT`), and **1 Router** (`1941` or `2811`) onto the workspace.
3. Wire the devices using **copper straight-through** cables:
   - PC0 → Switch0 (Fa0/1)
   - PC1 → Switch0 (Fa0/2)
   - Switch0 → Router0 (GigabitEthernet0/0)
   - PC2 → Switch1 (Fa0/1)
   - PC3 → Switch1 (Fa0/2)
   - Switch1 → Router0 (GigabitEthernet0/1)
4. Configure each PC's IP address, subnet mask and default gateway as per the table above (**Desktop → IP Configuration**).
5. Configure the router (see commands below).
6. Test intra-subnet connectivity and inter-subnet connectivity using `ping`.

## Router Configuration Commands

Open the router's **CLI** tab and enter the following:

```cisco
Router> enable
Router# configure terminal

! Set a hostname
Router(config)# hostname R1

! Configure the interface that connects to Subnet A (10.10.10.0/24)
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 10.10.10.10 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Configure the interface that connects to Subnet B (20.20.20.0/24)
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip address 20.20.20.10 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Return to privileged EXEC and save the configuration
R1(config)# end
R1# copy running-config startup-config
```

> **Note:** Because both subnets are *directly connected* to the router, no static routing or dynamic routing protocol is needed — the router automatically installs both `10.10.10.0/24` and `20.20.20.0/24` in its routing table.

## Verification Commands

```cisco
R1# show ip interface brief     ! both interfaces should be "up / up"
R1# show ip route               ! shows the two directly-connected networks (C)
R1# show running-config
```

## Connectivity Tests

### 1. Intra-subnet ping (works even without the router)
```
PC0> ping 10.10.10.2        ! PC0 → PC1   ✔
PC2> ping 20.20.20.2        ! PC2 → PC3   ✔
```

### 2. Inter-subnet ping (only works *after* the router is configured)
```
PC0> ping 20.20.20.1        ! PC0 → PC2   ✔
PC0> ping 20.20.20.2        ! PC0 → PC3   ✔
```

If the router interfaces are down, or the PCs are missing their default gateway, the inter-subnet ping **fails** — which is exactly what the simulation panel in `screenshots/02-inter-subnet-ping.png` shows for the initial attempt before the router was fully configured.

## Expected Output

```
PC> ping 20.20.20.1

Pinging 20.20.20.1 with 32 bytes of data:
Request timed out.
Reply from 20.20.20.1: bytes=32 time<1ms TTL=127
Reply from 20.20.20.1: bytes=32 time<1ms TTL=127
Reply from 20.20.20.1: bytes=32 time<1ms TTL=127

Ping statistics for 20.20.20.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
```

The first packet is typically lost due to ARP resolution across the router; subsequent packets succeed. Note the **TTL = 127** (instead of 128) — confirmation that the packet crossed exactly one router on its way.

## Observations

1. Hosts in the **same subnet** can communicate without a router (they use the switch only).
2. Hosts in **different subnets** cannot communicate until:
   - The router interfaces are assigned IPs in those subnets and brought up, **and**
   - Each PC has its default gateway pointing to the router interface in its own subnet.
3. A router with directly connected subnets does *not* need any static or dynamic routes — those networks are automatically known to the router.
4. The TTL value in the ping reply drops by 1 for each router hop, which can be used to count how many routers a packet traversed.

## Conclusion

An initial router configuration was successfully performed in Cisco Packet Tracer. The router, with one interface in each of two subnets (10.10.10.0/24 and 20.20.20.0/24), correctly forwarded packets between them, enabling inter-subnet communication between four PCs. This is the fundamental behaviour that makes the Internet — a network of networks — possible.
