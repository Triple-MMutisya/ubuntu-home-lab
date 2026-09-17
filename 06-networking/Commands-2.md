48. Check Network Services
systemctl --type=service | grep -i network
49. Network Logs
journalctl -u NetworkManager
journalctl -u NetworkManager --since today
50. Check Interface Errors
ip -s link
sudo ethtool -S eth0
51. Find Network Interface Names
ls /sys/class/net/
ip -br link
52. Check IP Address of Specific Interface
ip addr show eth0
ip addr show ens33
53. Check IPv4 Only
ip -4 addr show
54. Check IPv6 Only
ip -6 addr show
55. Test Gateway, Internet and DNS
ping -c 4 192.168.56.1
ping -c 4 1.1.1.1
ping -c 4 google.com
56. Check Complete Network Information
ip addr
ip route
resolvectl status
57. Check Network Connectivity Step-by-Step
ping -c 4 127.0.0.1
ping -c 4 GATEWAY_IP
ping -c 4 OTHER_LAB_VM_IP
ping -c 4 1.1.1.1
ping -c 4 google.com
resolvectl query google.com
58. SSH Networking
ssh username@192.168.56.20
ssh -p 22 username@192.168.56.20
59. Check SSH Port
ss -tulpn | grep :22
60. Test SSH Connectivity
nc -zv 192.168.56.20 22
61. Network Troubleshooting Quick Check
ip -br addr
ip route
resolvectl status
ping -c 4 127.0.0.1
ping -c 4 GATEWAY_IP
ping -c 4 1.1.1.1
ping -c 4 google.com
ss -tulpn
ip neigh
sudo tcpdump -i eth0
