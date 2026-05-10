# Practical 9 — Remote Desktop Sharing within a LAN

## Aim
To demonstrate **remote management / remote desktop sharing within a LAN** in Cisco Packet Tracer by configuring a manageable Cisco switch with a management IP address (on VLAN 1) and verifying that any host on the same LAN can reach the switch's management interface and the other hosts in the LAN.

## Theory

**Remote desktop sharing within a LAN** refers to the ability of one computer to access and control another machine that is on the **same local area network**. The minimum infrastructure for this is:
1. A **Layer-2 switch** that has been given a **management IP** so it is reachable for remote configuration.
2. **End hosts** with IP addresses in the same subnet as the switch.
3. A working LAN where every host can ping every other host (and the switch).

In a real Windows environment this is then handled by enabling **Remote Desktop Connection** (RDP, port 3389) or a tool like VNC. In Packet Tracer (which does not have a real RDP server), the practical demonstrates the *prerequisites* for remote sharing: assigning the switch a management IP, configuring all PCs in the same subnet, and proving full intra-LAN connectivity using `ping`.

### Network used in this practical

- **Switch0** (`2960-24TT`) — manageable Cisco switch, assigned a management IP on VLAN 1
- **PC0**, **PC1** — two hosts in the same LAN

### Topology

```
              Switch0  (192.168.1.100)
              /     \
            PC0     PC1
       192.168.1.11  192.168.1.10
```

## IP Addressing

| Device | IP Address | Subnet Mask |
|--------|------------|-------------|
| Switch0 (VLAN 1) | 192.168.1.100 | 255.255.255.0 |
| PC0 | 192.168.1.11 | 255.255.255.0 |
| PC1 | 192.168.1.10 | 255.255.255.0 |

## Procedure

1. Open Cisco Packet Tracer.
2. Drag onto the workspace:
   - **1 Switch** (`2960-24TT`) → Switch0
   - **2 PCs** (`PC-PT`) → PC0, PC1
3. Connect each PC's `FastEthernet0` to a port on Switch0 using a **copper straight-through** cable.
4. Configure each PC's IP via **Desktop → IP Configuration** as per the IP table.
5. Open the Switch's **CLI** and configure a hostname and a management IP on VLAN 1:

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname Admin

Admin(config)# interface vlan 1
Admin(config-if)# ip address 192.168.1.100 255.255.255.0
Admin(config-if)# no shutdown
Admin(config-if)# exit

Admin(config)# end
Admin# copy running-config startup-config
```

6. Verify reachability from each PC.

## Verification

From **PC0** (`192.168.1.11`):

```
C:\>ping 192.168.1.100        ! PC0 → Switch management interface
Request timed out.
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255

C:\>ping 192.168.1.10         ! PC0 → PC1
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
```

From **PC1** (`192.168.1.10`):

```
C:\>ping 192.168.1.100        ! PC1 → Switch management interface
C:\>ping 192.168.1.11         ! PC1 → PC0
```

Both pings return successful replies, proving:
- The switch's management interface is reachable on the LAN — the switch itself can now be administered remotely.
- PC0 and PC1 can reach each other over the LAN — the precondition for any remote-desktop / file-sharing session between them.

## Expected Output

```
Pinging 192.168.1.100 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255
Reply from 192.168.1.100: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.100:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
```

`TTL = 255` for replies coming from the switch (Cisco IOS default), and `TTL = 128` for replies coming from a Windows PC — useful for distinguishing the source.

## Observations

1. After assigning an IP on VLAN 1, the switch becomes a manageable host on the LAN — any PC on the same subnet can reach it (and could `telnet` / `ssh` into it for remote configuration).
2. The first ping packet is typically lost while ARP resolves the MAC of the destination, but all subsequent packets succeed.
3. The switch's TTL (255) and the PC's TTL (128) differ because Cisco IOS and Windows use different default starting values — handy when diagnosing which device replied.
4. Within the LAN every host can reach every other host directly, with only Layer-2 switching — no router is required.
5. With this LAN in place, a real OS could now run Windows Remote Desktop, VNC, or similar tools on top — the network layer is already proven.

## Conclusion

A LAN consisting of one Cisco switch and two PCs was successfully built in Cisco Packet Tracer. The switch was assigned a management IP on VLAN 1, and bidirectional `ping` tests confirmed reachability between every PC and the switch and between the two PCs themselves — establishing the network-level foundation that any **remote desktop sharing within a LAN** depends upon.
