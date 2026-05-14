# Day 2: Azure Networking & VNet Peering

## Task: Setup Azure Network Infrastructure

Create the following resources:
- **Resource Group**: 1 RG
- **Virtual Network**: 1 VNet with IP range `10.0.0.0/16`
- **Subnets**:
  - Public subnet: `10.0.1.0/24`
  - Private subnet: `10.0.2.0/24`
- **Compute**: Deploy a VM in the private subnet with your application

---

## VNet Peering

### What is VNet Peering?

A way of privately connecting two Virtual Networks in Azure.

**Key Points:**
- Allows resources to communicate directly using private IP addresses
- Example: VNet A (`10.0.0.0/16`) ↔ VNet B (`10.1.0.0/16`)
- Uses Azure Virtual Router and Microsoft Backbone for routing

### Traffic Flow Diagram

``` 
VM-A (wants to reach DB in VNet B)
  ↓
VM checks destination IP
  ↓
Subnet gateway IP lookup (e.g., 10.0.1.1/24)
  ↓
Azure SDN route lookup
  ↓
Matches 10.1.0.0/16 → Peered VNet
  ↓
Traffic enters Microsoft Backbone
  ↓
Backbone routes to destination VNet fabric
  ↓
Remote VNet receives traffic (e.g., 10.1.1.0/24)
  ↓
NSG evaluation on remote subnet
  ↓
Traffic delivered to target VM
```

### Data Path Through Network Stack

```
Guest OS
  ↓
vNIC
  ↓
Hyper-V vSwitch
  ↓
VFP (Virtual Filtering Platform - policy + route)
  ↓
Azure Host Forwarder
  ↓
Overlay Encapsulation
  ↓
Regional Fabric
  ↓
Microsoft Backbone (if cross-region)
  ↓
Remote Fabric
  ↓
Remote Host
  ↓
Remote VFP
  ↓
Remote vSwitch
  ↓
Remote vNIC
  ↓
Guest OS
```

### Why VNet Peering?

1. **Security & Segmentation**
   - Production isolated from Dev environments
   - Frontend isolated from Database tier

2. **Multi-tier Architecture**
   - Organize applications across isolated networks

3. **Hub-Spoke Topology**
   - Central hub VNet connects to multiple spoke VNets

### Key Concepts to Explore

- [ ] Microsoft Backbone Infrastructure
- [ ] Real-world VNet peering use cases
- [ ] OSI Layer communication (Layer 1-7)
- [ ] VLAN operation at Layer 2 and Layer 3
