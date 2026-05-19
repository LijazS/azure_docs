# Hub-spoke vnet peering uising firewalls

## do these 

1. Make 4 vnets
- vnet A - 10.0.0.0/16
- vnet B - 11.0.0.0/16
- vnet C - 12.0.0.0/16
- vnet D - 13.0.0.0/16

vnet-D is the hub

---

2.  
peer A <-> D
peer B <-> D
etc
-> Allow forward from 

---

3. make azure firewall subnet inside Vnet D

4. deploy the azure firewall inside of that subnet
    -> public ip is needed even if its just for internal hub to spoke. thats just azures design


5. add firewall rules to allow ur source and destination ip address range communincations

```
A ↔ B
10.0.0.0/16 → 11.0.0.0/16
11.0.0.0/16 → 10.0.0.0/16

B ↔ C
11.0.0.0/16 → 12.0.0.0/16
11.0.0.0/16 → 11.0.0.0/16

A ↔ C
12.0.0.0/16 → 10.0.0.0/16
10.0.0.0/16 → 12.0.0.0/16
```


6. make routing tables for all subnets that sends the packets the ip address of the firewall.

VNet-A (10.0.0.0/16) Route Table:
11.0.0.0/16 → Firewall IP ( eg- 13.0.0.4 )
12.0.0.0/16 → Firewall IP

etc ....

## Desgn

```
VM-A sends packet to 12.0.0.5
   ↓
Route Table says:
Send to Firewall (11.0.0.4)
   ↓
Firewall receives packet
   ↓
Checks:
- Source IP?
- Destination IP?
- Port?
- Protocol?
- Rule match?
   ↓
Allowed?
   ↓ Yes
Forward
   ↓
VM-C

```

Notes :- Route tables apply to traffic ORIGINATING FROM resources inside that subnet




IF USING NVA VM

1. enable forwarding in the NIC

2. sudo nano /etc/sysctl.conf

Uncomment/add:

net.ipv4.ip_forward=1

Apply:

sudo sysctl -p

cat /proc/sys/net/ipv4/ip_forward   

should return 1

3. Optional static routes:

sudo ip route add 10.0.0.0/16 via 13.0.1.1
sudo ip route add 11.0.0.0/16 via 13.0.1.1
sudo ip route add 12.0.0.0/16 via 13.0.1.1


4. add iptables  

sudo iptables -P FORWARD ACCEPT
<!-- sudo iptables -A FORWARD -s 10.0.0.0/16 -j ACCEPT
sudo iptables -A FORWARD -s 11.0.0.0/16 -j ACCEPT
sudo iptables -A FORWARD -s 12.0.0.0/16 -j ACCEPT
sudo iptables -A FORWARD -d 10.0.0.0/16 -j ACCEPT
sudo iptables -A FORWARD -d 11.0.0.0/16 -j ACCEPT -->
sudo                                                            

More Secure Version:

sudo iptables -P FORWARD DROP

sudo iptables -A FORWARD \
  -s 10.0.0.0/16 \
  -d 11.0.0.0/16 \
  -j ACCEPT

sudo iptables -A FORWARD \
  -s 11.0.0.0/16 \
  -d 10.0.0.0/16 \
  -j ACCEPT