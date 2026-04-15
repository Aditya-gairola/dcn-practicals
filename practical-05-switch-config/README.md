# Practical 5 — Initial Switch Configuration

## Aim
To perform an initial configuration of a Cisco switch in Packet Tracer — setting the hostname, enable/console/vty passwords, banner message, management IP, and saving the configuration to NVRAM.

## Theory

A Cisco switch ships from the factory with a blank configuration. Before deploying it in a production network, an administrator performs an **initial configuration** (also called *baseline configuration*) that typically includes:

1. Assigning a meaningful **hostname**.
2. Setting a **privileged EXEC (enable) password** to restrict access to configuration mode.
3. Protecting the **console port** with a password (physical access).
4. Protecting **VTY lines** with a password (Telnet/SSH access).
5. Displaying a legal **banner** (Message of the Day).
6. Assigning a **management IP address** to a VLAN interface (usually VLAN 1) for remote management.
7. **Encrypting plaintext passwords** in the running configuration.
8. **Saving** the running configuration to the startup configuration (NVRAM) so it survives a reboot.

### Cisco IOS Command Modes

| Mode | Prompt | Entered by |
|------|--------|-----------|
| User EXEC | `Switch>` | Login |
| Privileged EXEC | `Switch#` | `enable` |
| Global Configuration | `Switch(config)#` | `configure terminal` |
| Interface Configuration | `Switch(config-if)#` | `interface <name>` |
| Line Configuration | `Switch(config-line)#` | `line console 0` / `line vty 0 15` |

Exit from any mode with `exit` (one level up) or `end` (back to privileged EXEC).

## Procedure

1. Open Cisco Packet Tracer.
2. Drag a switch (**2960-24TT**) onto the workspace.
3. Drag a PC and connect it to the switch's **Console** port using the **Console Cable** (light-blue cable). The PC's **RS-232** port goes to the switch's **Console** port.
4. On the PC, go to **Desktop → Terminal**, accept the default settings (9600 baud, 8-N-1, no flow control), and press **OK**.
5. Press **Enter** to get the `Switch>` prompt.
6. Enter the configuration commands below one by one.

## Configuration Commands

```cisco
! Enter privileged EXEC mode
Switch> enable
Switch#

! Enter global configuration mode
Switch# configure terminal
Switch(config)#

! 1. Set the hostname
Switch(config)# hostname S1
S1(config)#

! 2. Set the enable secret (encrypted privileged-mode password)
S1(config)# enable secret class123

! 3. Set the console-port password
S1(config)# line console 0
S1(config-line)# password cisco123
S1(config-line)# login
S1(config-line)# exit

! 4. Set the VTY (Telnet) password for lines 0-15
S1(config)# line vty 0 15
S1(config-line)# password telnet123
S1(config-line)# login
S1(config-line)# exit

! 5. Encrypt all plaintext passwords in the configuration
S1(config)# service password-encryption

! 6. Set a login banner (Message of the Day)
S1(config)# banner motd #Authorized access only!#

! 7. Assign a management IP address to VLAN 1
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.250 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit

! 8. Set a default gateway (useful for remote management)
S1(config)# ip default-gateway 192.168.1.1

! Exit to privileged EXEC
S1(config)# end
S1#

! 9. Save the running configuration to NVRAM
S1# copy running-config startup-config
Destination filename [startup-config]?  <press Enter>
Building configuration...
[OK]
```

## Verification Commands

```cisco
! View the current running configuration
S1# show running-config

! View the startup configuration
S1# show startup-config

! View interface status
S1# show ip interface brief

! View the VLAN 1 management interface
S1# show interface vlan 1

! View the MAC address table
S1# show mac-address-table
```

### Testing remote access

1. Connect a **PC** to a FastEthernet port of the switch using a straight-through cable.
2. Configure the PC with IP `192.168.1.10 / 255.255.255.0`.
3. From the PC's **Command Prompt**, `ping 192.168.1.250` — should succeed.
4. Telnet to the switch: `telnet 192.168.1.250`. It should prompt for the VTY password (`telnet123`), then for the enable secret (`class123`) when `enable` is typed.

## Expected Output (excerpt from `show running-config`)

```
!
hostname S1
!
enable secret 5 $1$mERr$...
!
banner motd ^CAuthorized access only!^C
!
interface Vlan1
 ip address 192.168.1.250 255.255.255.0
!
ip default-gateway 192.168.1.1
!
line con 0
 password 7 0822455D0A16
 login
!
line vty 0 4
 password 7 02050D480809
 login
line vty 5 15
 password 7 02050D480809
 login
!
end
```

## Observations

1. The hostname appears in the prompt as soon as it is set.
2. After `service password-encryption`, plaintext passwords in the configuration are stored as encrypted (type 7) strings.
3. The enable secret is stored as an MD5 hash (type 5) — stronger than type 7.
4. The management IP on VLAN 1 allows remote access via Telnet.
5. The configuration persists across reboots only after `copy running-config startup-config`.

## Conclusion

An initial configuration of a Cisco 2960 switch was successfully performed. The switch now has a hostname, console and VTY passwords, an enable secret, a banner, and a management IP, and the configuration has been saved to NVRAM. This baseline configuration is a prerequisite for deploying the switch in any production network.

 
