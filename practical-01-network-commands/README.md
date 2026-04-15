# Practical 1 — Basic Network Commands and Network Configuration Commands

## Aim
To study and execute basic network commands and network configuration commands used for diagnosing, troubleshooting, and configuring a computer network.

## Theory

Every operating system ships with a set of built-in command-line utilities that let a user inspect the network stack, test connectivity, and configure adapters. These commands operate across layers of the TCP/IP model:

- **Network layer commands** (IP configuration, reachability): `ipconfig`, `ifconfig`, `ping`, `tracert`, `route`
- **Data-link layer commands** (MAC/ARP tables): `arp`, `getmac`
- **Application / name-resolution commands**: `nslookup`, `hostname`
- **Session / transport status**: `netstat`

Understanding these commands is essential for any network administrator because they are the first line of diagnosis when a network issue is reported.

## Commands Studied

### 1. `ipconfig` (Windows) / `ifconfig` or `ip addr` (Linux)
Displays the IP address, subnet mask, and default gateway of all active network interfaces.

```bash
# Windows
ipconfig
ipconfig /all         # Detailed information including MAC, DHCP, DNS
ipconfig /release     # Releases the current DHCP lease
ipconfig /renew       # Requests a new DHCP lease
ipconfig /flushdns    # Clears the DNS resolver cache

# Linux
ifconfig
ip addr show
```

**Sample output (Windows):**
```
Wireless LAN adapter Wi-Fi:
   Connection-specific DNS Suffix . : local
   IPv4 Address . . . . . . . . . . : 192.168.1.15
   Subnet Mask  . . . . . . . . . . : 255.255.255.0
   Default Gateway  . . . . . . . . : 192.168.1.1
```

### 2. `ping`
Sends ICMP Echo Request packets to a target host to test reachability and measure round-trip time.

```bash
ping 8.8.8.8
ping www.google.com
ping -n 10 192.168.1.1    # Windows: send 10 packets
ping -c 10 192.168.1.1    # Linux: send 10 packets
ping -t 192.168.1.1       # Windows: continuous ping
```

**Sample output:**
```
Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=18ms TTL=117
Reply from 8.8.8.8: bytes=32 time=19ms TTL=117

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 18ms, Maximum = 20ms, Average = 19ms
```

### 3. `tracert` (Windows) / `traceroute` (Linux)
Shows the path (list of routers/hops) a packet takes to reach a destination.

```bash
tracert www.google.com        # Windows
traceroute www.google.com     # Linux
```

**Sample output:**
```
Tracing route to www.google.com [142.250.183.100]
over a maximum of 30 hops:

  1     1 ms     1 ms     1 ms  192.168.1.1
  2     8 ms     7 ms     8 ms  10.0.0.1
  3    15 ms    14 ms    15 ms  isp-gateway.net
  ...
 10    19 ms    20 ms    18 ms  142.250.183.100
```

### 4. `nslookup`
Queries DNS servers to resolve a domain name to its IP address (and vice-versa).

```bash
nslookup www.google.com
nslookup 8.8.8.8                # Reverse lookup
nslookup www.google.com 1.1.1.1 # Use a specific DNS server
```

**Sample output:**
```
Server:  dns.google
Address: 8.8.8.8

Non-authoritative answer:
Name:    www.google.com
Addresses: 2404:6800:4009:820::2004
           142.250.183.100
```

### 5. `arp`
Displays and modifies the ARP (Address Resolution Protocol) cache — the mapping of IP addresses to MAC addresses on the local network.

```bash
arp -a            # Show the ARP cache
arp -d 192.168.1.1   # Delete an entry
```

**Sample output:**
```
Interface: 192.168.1.15 --- 0xd
  Internet Address      Physical Address      Type
  192.168.1.1           aa-bb-cc-dd-ee-ff     dynamic
  192.168.1.20          11-22-33-44-55-66     dynamic
```

### 6. `netstat`
Displays active network connections, listening ports, routing tables, and per-protocol statistics.

```bash
netstat -a      # All connections and listening ports
netstat -n      # Addresses and ports in numerical form
netstat -r      # Routing table
netstat -o      # Show owning process ID (Windows)
```

### 7. `hostname`
Displays the computer's host name.

```bash
hostname
```

### 8. `getmac` (Windows) / `ip link` (Linux)
Displays the MAC address of all NICs on the machine.

```bash
getmac /v         # Windows, verbose
ip link show      # Linux
```

### 9. `route`
Displays and modifies the local IP routing table.

```bash
route print                      # Windows
ip route                         # Linux
route add 10.0.0.0 mask 255.0.0.0 192.168.1.1   # Add a static route
```

### 10. `telnet` (now disabled by default)
Tests TCP-level connectivity to a specific port.

```bash
telnet www.google.com 80
```


## Observations

| Command | Purpose | Layer |
|---------|---------|-------|
| `ipconfig` / `ifconfig` | Show IP, mask, gateway | Network |
| `ping` | Test reachability | Network (ICMP) |
| `tracert` / `traceroute` | Trace path of packets | Network (ICMP) |
| `nslookup` | Resolve domain → IP | Application (DNS) |
| `arp` | IP ↔ MAC mapping | Data Link |
| `netstat` | Active connections & listening ports | Transport |
| `hostname` | Local machine name | — |
| `getmac` | Local MAC addresses | Data Link |
| `route` | View/modify routing table | Network |

## Conclusion

The basic network commands were studied and successfully executed. These commands form the foundation of network diagnostics — `ipconfig` for local configuration, `ping` and `tracert` for reachability, `nslookup` for name resolution, `arp` for L2 mapping, and `netstat` for transport-level visibility.

 
