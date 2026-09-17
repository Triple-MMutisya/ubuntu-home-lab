# Linux Home Lab — Static IP Configuration & Connectivity Testing

## Lab 4.2 — Configure a Static IP with Netplan

This lab demonstrates how to identify a Linux network interface, configure a static IPv4 address using **Netplan**, validate the configuration, apply it, and verify network connectivity.

> **Lab Objective:**
> Configure a static IP address on an Ubuntu virtual machine and test connectivity from the local system to the gateway, other lab machines, the Internet, and DNS.

---

## 1. Identify the Network Interface

Before modifying Netplan, identify the network interface available on your Ubuntu VM.

```bash
ip -br addr
```

Example output:

```text
lo        UNKNOWN        127.0.0.1/8
ens33     UP             192.168.56.20/24
```

In this example:

* `lo` = Loopback interface
* `ens33` = Ethernet interface
* `192.168.56.20/24` = Current IPv4 address

### Important

Do **not** blindly copy `ens33`.

Your interface could have a different name, such as:

```text
ens33
enp0s3
eth0
enp1s0
```

Always use the interface name returned by:

```bash
ip -br addr
```

---

# 2. View Current Netplan Configuration

List the Netplan configuration files:

```bash
ls -lah /etc/netplan/
```

You can view the contents of the YAML configuration files with:

```bash
sudo cat /etc/netplan/*.yaml
```

For example:

```text
/etc/netplan/00-installer-config.yaml
```

You can also inspect the directory with:

```bash
ls /etc/netplan
```

Then open the configuration file:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

> **Note:** Your file may have a different name. Use the filename shown by `ls /etc/netplan`.

---

# 3. Configure a Static IP Address

Create or edit a Netplan configuration file.

For example:

```bash
sudo nano /etc/netplan/01-lab.yaml
```

Example static IP configuration:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: false
      addresses:
        - 192.168.56.20/24
      routes:
        - to: default
          via: 192.168.56.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

### Configuration Breakdown

| Setting            | Purpose                                      |
| ------------------ | -------------------------------------------- |
| `network:`         | Defines the network configuration            |
| `version: 2`       | Uses Netplan network configuration version 2 |
| `ethernets:`       | Defines Ethernet interfaces                  |
| `ens33:`           | Network interface being configured           |
| `dhcp4: false`     | Disables IPv4 DHCP                           |
| `addresses:`       | Defines the static IP address                |
| `192.168.56.20/24` | Static IP with `/24` prefix                  |
| `routes:`          | Defines routing information                  |
| `to: default`      | Creates the default route                    |
| `via:`             | Specifies the gateway                        |
| `nameservers:`     | Defines DNS servers                          |
| `1.1.1.1`          | Cloudflare DNS                               |
| `8.8.8.8`          | Google DNS                                   |

---

# 4. Adjust the Configuration to Your Lab

The example above is only a demonstration.

Change the following values to match your actual lab:

```text
ens33
192.168.56.20/24
192.168.56.1
1.1.1.1
8.8.8.8
```

For example:

```yaml
addresses:
  - YOUR_STATIC_IP/PREFIX
```

and:

```yaml
routes:
  - to: default
    via: YOUR_GATEWAY_IP
```

Your interface name, IP address, subnet/prefix, gateway, and DNS servers must match your network design.

---

# 5. Example DHCP Configuration

A DHCP-based Netplan configuration may look like this:

```yaml
network:
  ethernets:
    eth0:
      dhcp4: true
      dhcp6: true
      match:
        macaddress: 00:15:5d:01:f6:00
      set-name: eth0
  version: 2
```

In this example:

```yaml
dhcp4: true
```

means the system obtains an IPv4 address automatically from a DHCP server.

```yaml
dhcp6: true
```

enables DHCP for IPv6.

The `match` section identifies the network interface using its MAC address:

```yaml
match:
  macaddress: 00:15:5d:01:f6:00
```

The interface is then assigned the name:

```yaml
set-name: eth0
```

---

# 6. Netplan YAML Formatting

Netplan uses **YAML**, so indentation is important.

Correct:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: false
```

Incorrect indentation can cause errors.

For example:

```yaml
network:
version: 2
ethernets:
```

will not work correctly.

### Important YAML Rule

Use spaces rather than tabs.

A common structure is:

```text
network
 └── ethernets
      └── interface
           ├── dhcp4
           ├── addresses
           ├── routes
           └── nameservers
```

---

# 7. Validate the Configuration

Before applying a new network configuration, test it with:

```bash
sudo netplan try
```

`netplan try` temporarily applies the configuration and allows you to confirm that networking still works.

If the configuration is correct, confirm it.

If the configuration is incorrect or connectivity is lost, Netplan can automatically roll back the change.

> **Important:** When changing the network configuration of a remote server, use `netplan try` first whenever possible. This helps prevent locking yourself out because of an incorrect network configuration.

---

# 8. Apply the Configuration

If the configuration has been validated successfully:

```bash
sudo netplan apply
```

This applies the Netplan configuration permanently.

---

# 9. Verify the IP Address

Check the interface and assigned IP address:

```bash
ip -br addr
```

Example:

```text
lo        UNKNOWN        127.0.0.1/8
ens33     UP             192.168.56.20/24
```

Confirm that the expected static IP is assigned.

---

# 10. Verify the Routing Table

Run:

```bash
ip route
```

Example:

```text
default via 192.168.56.1 dev ens33
192.168.56.0/24 dev ens33 proto kernel scope link src 192.168.56.20
```

The important line is:

```text
default via 192.168.56.1 dev ens33
```

This indicates that `192.168.56.1` is the default gateway.

---

# 11. Verify DNS Configuration

Run:

```bash
resolvectl status
```

Look for the DNS servers configured for your interface.

For example:

```text
DNS Servers: 1.1.1.1
             8.8.8.8
```

---

# Lab 4.3 — Connectivity Testing

After configuring the network, perform connectivity tests in a logical order.

---

## 12. Test the Local Loopback Interface

Test the local TCP/IP stack:

```bash
ping -c 4 127.0.0.1
```

Expected result:

```text
64 bytes from 127.0.0.1
```

### What this tests

The loopback test verifies that the local networking stack is functioning.

---

# 13. Test the Default Gateway

Replace `GATEWAY_IP` with your actual gateway.

```bash
ping -c 4 GATEWAY_IP
```

Example:

```bash
ping -c 4 192.168.56.1
```

### What this tests

This checks connectivity between your Ubuntu VM and the local network gateway.

If this fails, investigate:

* Network adapter
* Virtual switch
* VLAN
* IP address
* Subnet/prefix
* Default gateway
* Firewall
* Virtual machine networking

---

# 14. Test Another Lab VM

Test connectivity to another machine on your lab network:

```bash
ping -c 4 192.168.56.30
```

Replace the address with the IP address of another VM.

### What this tests

This verifies communication between systems on the same lab network.

---

# 15. Test Internet Connectivity Using an IP Address

Test a known public IP:

```bash
ping -c 4 1.1.1.1
```

If this works but domain names do not work, the problem is likely related to DNS rather than basic Internet routing.

---

# 16. Test DNS Resolution

Test a domain name:

```bash
ping -c 4 google.com
```

You can also directly query DNS:

```bash
resolvectl query google.com
```

Example:

```text
google.com: 142.250.x.x
```

This verifies that your system can resolve a domain name to an IP address.

---

# 17. Connectivity Troubleshooting Logic

Use the following sequence when troubleshooting:

```text
127.0.0.1
     │
     ▼
Gateway
     │
     ▼
Another Lab VM
     │
     ▼
Internet IP
     │
     ▼
Domain Name / DNS
```

### Test 1 — Local Stack

```bash
ping -c 4 127.0.0.1
```

If this fails:

```text
Investigate local TCP/IP stack.
```

---

### Test 2 — Gateway

```bash
ping -c 4 GATEWAY_IP
```

If the gateway fails:

```text
Possible local network,
VLAN, interface, subnet,
gateway, or VM networking problem.
```

---

### Test 3 — Another Lab VM

```bash
ping -c 4 192.168.56.30
```

If the gateway works but another VM fails:

```text
Investigate VM-to-VM connectivity,
firewall rules, subnet configuration,
or virtual switch configuration.
```

---

### Test 4 — Internet IP

```bash
ping -c 4 1.1.1.1
```

If the gateway works but `1.1.1.1` fails:

```text
Possible routing or Internet connectivity problem.
```

---

### Test 5 — DNS

```bash
ping -c 4 google.com
```

and:

```bash
resolvectl query google.com
```

If `1.1.1.1` works but the domain fails:

```text
Likely DNS resolution problem.
```

---

# 18. Useful Network Commands

| Command                            | Purpose                                    |
| ---------------------------------- | ------------------------------------------ |
| `ip -br addr`                      | Display interfaces and IP addresses        |
| `ip addr`                          | Display detailed interface information     |
| `ip route`                         | Display routing table                      |
| `ip link`                          | Display network interfaces and their state |
| `ls /etc/netplan/`                 | List Netplan configuration files           |
| `sudo cat /etc/netplan/*.yaml`     | Display Netplan configuration              |
| `sudo nano /etc/netplan/file.yaml` | Edit Netplan configuration                 |
| `sudo netplan try`                 | Safely test configuration                  |
| `sudo netplan apply`               | Apply Netplan configuration                |
| `resolvectl status`                | Display DNS configuration                  |
| `resolvectl query google.com`      | Test DNS resolution                        |
| `ping -c 4 IP`                     | Test network connectivity                  |

---

# 19. Lab Verification Checklist

Use this checklist to document the completed lab:

* [ ] Identified the network interface
* [ ] Checked `/etc/netplan/`
* [ ] Reviewed the existing Netplan configuration
* [ ] Configured a static IPv4 address
* [ ] Configured the correct subnet/prefix
* [ ] Configured the default gateway
* [ ] Configured DNS servers
* [ ] Validated configuration with `netplan try`
* [ ] Applied configuration with `netplan apply`
* [ ] Verified IP address with `ip -br addr`
* [ ] Verified routing with `ip route`
* [ ] Verified DNS with `resolvectl status`
* [ ] Tested loopback connectivity
* [ ] Tested gateway connectivity
* [ ] Tested another lab VM
* [ ] Tested Internet IP connectivity
* [ ] Tested DNS resolution

---

# 20. Evidence / Screenshots for GitHub

For your Linux Home Lab portfolio, capture screenshots showing:

### Screenshot 1 — Interface

```bash
ip -br addr
```

### Screenshot 2 — Netplan Configuration

```bash
sudo cat /etc/netplan/*.yaml
```

### Screenshot 3 — Static IP

```bash
ip -br addr
```

Show that the expected static IP is assigned.

### Screenshot 4 — Routing

```bash
ip route
```

Show the default gateway.

### Screenshot 5 — DNS

```bash
resolvectl status
```

### Screenshot 6 — Connectivity

```bash
ping -c 4 127.0.0.1
ping -c 4 GATEWAY_IP
ping -c 4 192.168.56.30
ping -c 4 1.1.1.1
ping -c 4 google.com
```

### Screenshot 7 — DNS Query

```bash
resolvectl query google.com
```

---

# 21. Skills Demonstrated

This lab demonstrates practical Linux networking skills including:

* Linux network interface identification
* IPv4 addressing
* CIDR/prefix notation
* Static IP configuration
* DHCP configuration
* Netplan
* Default gateways
* Routing tables
* DNS configuration
* DNS troubleshooting
* Network connectivity testing
* Virtual machine networking
* Basic network troubleshooting methodology

---

## Lab Summary

The overall troubleshooting workflow used in this lab is:

```text
Identify Interface
       ↓
Check Netplan
       ↓
Configure IP
       ↓
Configure Gateway
       ↓
Configure DNS
       ↓
netplan try
       ↓
netplan apply
       ↓
Verify IP
       ↓
Verify Route
       ↓
Verify DNS
       ↓
Test Gateway
       ↓
Test Lab VM
       ↓
Test Internet IP
       ↓
Test Domain/DNS
```

The key principle is to troubleshoot networking **from the bottom up**:

```text
Local System
     ↓
Network Interface
     ↓
Local Network
     ↓
Gateway
     ↓
Routing
     ↓
Internet
     ↓
DNS
```

This approach makes it easier to identify exactly where a connectivity problem occurs.

