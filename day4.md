# VPN Gateway

## IKEv2 Tunnel Establishment

### Phase 1: IKE SA Creation

Both VPN gateways:

- Negotiate:
  - Encryption algorithm
  - Hash algorithm
  - DH group
  - Authentication method
- Perform Diffie-Hellman exchange
- Derive a shared secret
- Generate symmetric encryption keys

#### Diffie-Hellman Example

1. User A and User B agree on public parameters:
   - `p = 23`
   - `g = 5`

2. User A combines their secret key `a` with the public parameters and sends the resulting public key `A`:

```text
A = 5^a mod 23
```

3. User A then combines `B` with their own secret key `a` to derive the shared secret:

```text
S = B^a mod 23
```

---

### Phase 2: IPsec SA Creation

Inside the encrypted IKE channel, both sides negotiate the actual IPsec tunnel parameters:

- ESP
- Encryption keys
- SPI values
- Lifetimes

This creates the `IPsec Security Associations`, and then actual data traffic can flow securely.

---

## Task

- Make a Load Balancer
- Create a VMSS
- Deploy the Organic application using Bootstrap
- Configure autoscaling rules to set min and max based on the metric
- Configure Azure Monitor to monitor traffic
- When traffic increases, send notifications by email
- To implement notifications, use Azure Service Bus with a subscription or topic-based pattern

## Implementation Flow

| Phase | Task |
| --- | --- |
| 1 | Create Resource Group |
| 2 | Create VMSS |
| 3 | Create Load Balancer |
| 4 | Deploy Organic Bootstrap App |
| 5 | Configure Autoscaling |
| 6 | Configure Azure Monitor |
| 7 | Create Alert Rules |
| 8 | Create Service Bus Namespace |
| 9 | Create Topic |
| 10 | Create Topic Subscription |
| 11 | Create Logic App |
| 12 | Connect Alert -> Service Bus |
| 13 | Send Email Notifications |
| 14 | Test Entire Flow |

## Notes

- Enabled Insights in monitoring for VMSS
- Created an alert rule
- Created a Service Bus namespace using the Standard tier
- Created a topic
- Opened the topic and created a subscription
- Created a Logic App
