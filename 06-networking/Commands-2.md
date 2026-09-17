# Ubuntu Networking Commands

## 1. Network Interfaces

```bash
ip -br addr
ip addr
ip link
ip link show
```

## 2. Check Interface Status

```bash
ip link show eth0
ip link show ens33
```

```bash
sudo ethtool eth0
```

## 3. MAC Address

```bash
ip link
ip addr
```

```bash
cat /sys/class/net/eth0/address
```

## 4. IPv4 Address

```bash
ip -4 addr
ip -4 addr show
ip -4 addr show eth0
```

## 5. IPv6 Address

```bash
ip -6 addr
ip -6 addr show
```

## 6. Routing Table

```bash
ip route
ip route show
ip -4 route
ip -6 route
```

## 7. Default Gateway

```bash
ip route | grep default
```

```bash
ip route get 1.1.1.1
```

## 8. DNS

```bash
resolvectl status
resolvectl dns
resolvectl query google.com
```

```bash
cat /etc/resolv.conf
```

## 9. Netplan Configuration

```bash
ls -lah /etc/netplan/
```

```bash
sudo cat /etc/netplan/*.yaml
```

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

```bash
sudo nano /etc/netplan/01-lab.yaml
```

## 10. Validate Netplan

```bash
sudo netplan try
```

## 11. Apply Netplan

```bash
sudo netplan apply
```

## 12. Generate Netplan Configuration

```bash
sudo netplan generate
```

## 13. NetworkManager Status

```bash
systemctl status NetworkManager
```

```bash
nmcli general status
nmcli device status
nmcli connection show
```

## 14. Network Interface Using NetworkManager

```bash
nmcli device
nmcli device show
```

```bash
nmcli device show eth0
```

## 15. Bring Interface Up

```bash
sudo ip link set eth0 up
```

## 16. Bring Interface Down

```bash
sudo ip link set eth0 down
```

## 17. Assign Temporary IP Address

```bash
sudo ip addr add 192.168.56.20/24 dev eth0
```

## 18. Remove IP Address

```bash
sudo ip addr del 192.168.56.20/24 dev eth0
```

## 19. Add Default Route

```bash
sudo ip route add default via 192.168.56.1
```

## 20. Remove Default Route

```bash
sudo ip route del default
```

## 21. Ping Localhost

```bash
ping -c 4 127.0.0.1
```

## 22. Ping Own Gateway

```bash
ping -c 4 192.168.56.1
```

## 23. Ping Another Lab VM

```bash
ping -c 4 192.168.56.30
```

## 24. Test Internet Connectivity

```bash
ping -c 4 1.1.1.1
```

```bash
ping -c 4 8.8.8.8
```

## 25. Test DNS

```bash
ping -c 4 google.com
```

```bash
resolvectl query google.com
```

## 26. Trace Network Path

```bash
traceroute 1.1.1.1
```

```bash
tracepath 1.1.1.1
```

## 27. Install Traceroute

```bash
sudo apt update
sudo apt install traceroute
```

## 28. Test DNS With Dig

```bash
dig google.com
```

```bash
dig google.com A
dig google.com MX
dig google.com NS
```

## 29. Install DNS Utilities

```bash
sudo apt update
sudo apt install dnsutils
```

## 30. DNS Lookup

```bash
nslookup google.com
```

## 31. Check Listening Ports

```bash
ss -tulpn
```

```bash
ss -tuln
```

```bash
ss -tulp
```

## 32. Check Specific Port

```bash
ss -tulpn | grep :22
```

```bash
ss -tulpn | grep :80
```

```bash
ss -tulpn | grep :443
```

## 33. Check Open Connections

```bash
ss -tun
```

```bash
ss -tp
```

## 34. Test TCP Port With Netcat

```bash
nc -zv 192.168.56.30 22
```

```bash
nc -zv 192.168.56.30 80
```

## 35. Install Netcat

```bash
sudo apt install netcat-openbsd
```

## 36. ARP / Neighbor Table

```bash
ip neigh
```

```bash
ip neigh show
```

## 37. Clear Neighbor Entry

```bash
sudo ip neigh del 192.168.56.1 dev eth0
```

## 38. Check Network Routes

```bash
ip route
```

```bash
ip route list
```

```bash
ip route get 8.8.8.8
```

## 39. Check Network Statistics

```bash
ip -s link
```

```bash
cat /proc/net/dev
```

## 40. Network Traffic

```bash
sudo tcpdump -i eth0
```

```bash
sudo tcpdump -i eth0 icmp
```

```bash
sudo tcpdump -i eth0 port 22
```

## 41. Install Tcpdump

```bash
sudo apt update
sudo apt install tcpdump
```

## 42. Check Hostname

```bash
hostname
```

```bash
hostnamectl
```

## 43. Change Hostname

```bash
sudo hostnamectl set-hostname ubuntu-lab
```

## 44. Check Hosts File

```bash
cat /etc/hosts
```

```bash
sudo nano /etc/hosts
```

## 45. Check DNS Configuration

```bash
cat /etc/resolv.conf
```

```bash
resolvectl status
```

## 46. Restart NetworkManager

```bash
sudo systemctl restart NetworkManager
```

## 47. Check NetworkManager

```bash
sudo systemctl status NetworkManager
```

## 48. Check Network Services

```bash
systemctl --type=service | grep -i network
```

## 49. Network Logs

```bash
journalctl -u NetworkManager
```

```bash
journalctl -u NetworkManager --since today
```

## 50. Check Interface Errors

```bash
ip -s link
```

```bash
sudo ethtool -S eth0
```

## 51. Find Network Interface Names

```bash
ls /sys/class/net/
```

```bash
ip -br link
```

## 52. Check IP Address of Specific Interface

```bash
ip addr show eth0
```

```bash
ip addr show ens33
```

## 53. Check IPv4 Only

```bash
ip -4 addr show
```

## 54. Check IPv6 Only

```bash
ip -6 addr show
```

## 55. Test Gateway, Internet and DNS

```bash
ping -c 4 192.168.56.1
ping -c 4 1.1.1.1
ping -c 4 google.com
```

## 56. Check Complete Network Information

```bash
ip addr
ip route
resolvectl status
```

## 57. Check Network Connectivity Step-by-Step

```bash
ping -c 4 127.0.0.1
```

```bash
ping -c 4 GATEWAY_IP
```

```bash
ping -c 4 OTHER_LAB_VM_IP
```

```bash
ping -c 4 1.1.1.1
```

```bash
ping -c 4 google.com
```

```bash
resolvectl query google.com
```

## 58. SSH Networking

```bash
ssh username@192.168.56.20
```

```bash
ssh -p 22 username@192.168.56.20
```

## 59. Check SSH Port

```bash
ss -tulpn | grep :22
```

## 60. Test SSH Connectivity

```bash
nc -zv 192.168.56.20 22
```

## 61. Network Troubleshooting Quick Check

```bash
ip -br addr
```

```bash
ip route
```

```bash
resolvectl status
```

```bash
ping -c 4 127.0.0.1
```

```bash
ping -c 4 GATEWAY_IP
```

```bash
ping -c 4 1.1.1.1
```

```bash
ping -c 4 google.com
```

```bash
ss -tulpn
```

```bash
ip neigh
```

```bash
sudo tcpdump -i eth0
```
