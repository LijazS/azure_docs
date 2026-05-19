# Hub-and-Spoke VNet Peering Using Firewalls

## Tasks

1. Create 4 VNets:
   - VNet A: `10.0.0.0/16`
   - VNet B: `11.0.0.0/16`
   - VNet C: `12.0.0.0/16`
   - VNet D: `13.0.0.0/16`

`VNet D` is the hub.

2. Create peering between the hub and each spoke:
   - `A <-> D`
   - `B <-> D`
   - `C <-> D`
   - Allow forwarded traffic in peering settings.

3. Create an `AzureFirewallSubnet` inside `VNet D`.

4. Deploy Azure Firewall into that subnet.
   - A public IP is required even if the firewall is only being used for internal hub-and-spoke routing. That is part of Azure's design.

5. Add firewall rules to allow source and destination IP range communication.

```text
A <-> B
10.0.0.0/16 -> 11.0.0.0/16
11.0.0.0/16 -> 10.0.0.0/16

B <-> C
11.0.0.0/16 -> 12.0.0.0/16
12.0.0.0/16 -> 11.0.0.0/16

A <-> C
10.0.0.0/16 -> 12.0.0.0/16
12.0.0.0/16 -> 10.0.0.0/16
```

6. Create route tables for all subnets so packets are sent to the firewall IP.

Example for `VNet A (10.0.0.0/16)` route table:

```text
11.0.0.0/16 -> Firewall IP (for example: 13.0.0.4)
12.0.0.0/16 -> Firewall IP
```

Repeat the same idea for the other VNets.

## Design

```text
VM-A sends packet to 12.0.0.5
   |
   v
Route table says:
Send to Firewall (13.0.0.4)
   |
   v
Firewall receives packet
   |
   v
Checks:
- Source IP
- Destination IP
- Port
- Protocol
- Rule match
   |
   v
Allowed?
   |
   v
Yes -> Forward
   |
   v
VM-C
```

## Notes

- Route tables apply to traffic originating from resources inside that subnet.

## `If Using an NVA VM`

1. Enable IP forwarding on the NIC.

2. Enable IP forwarding in the OS:

```bash
sudo nano /etc/sysctl.conf
```

Uncomment or add:

```conf
net.ipv4.ip_forward=1
```

Apply the change:

```bash
sudo sysctl -p
cat /proc/sys/net/ipv4/ip_forward
```

It should return:

```text
1
```

3. Optional static routes:

```bash
sudo ip route add 10.0.0.0/16 via 13.0.1.1
sudo ip route add 11.0.0.0/16 via 13.0.1.1
sudo ip route add 12.0.0.0/16 via 13.0.1.1
```

4. Add `iptables` rules.

Basic forwarding approach:

```bash
sudo iptables -P FORWARD ACCEPT
```

More secure version:

```bash
sudo iptables -P FORWARD DROP

sudo iptables -A FORWARD \
  -s 10.0.0.0/16 \
  -d 11.0.0.0/16 \
  -j ACCEPT

sudo iptables -A FORWARD \
  -s 11.0.0.0/16 \
  -d 10.0.0.0/16 \
  -j ACCEPT
```
