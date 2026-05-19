# VPN Gateway

- ## IKEv2 tunnel establishment
### Phase 1 — IKE SA creation

Both VPN gateways:

Negotiate:
encryption algorithm
hash algorithm
DH group
authentication method
Perform Diffie–Hellman exchange
Derive shared secret
Generate symmetric encryption keys


1. user a and b agree on public parameters : p = 23 and g = 5

2. User a combines their secret key (a) with parameters and sends the resulting pubic key (A) to user 2
A = 5**a mod 23

3. then user 1 combines B with their own secret key (a)
S = B**a mod 23




---


### Phase 2 — IPsec SA creation

Inside the encrypted IKE channel:

They negotiate actual IPsec tunnel parameters:

ESP
encryption keys
SPI values
lifetimes

This creates:

IPsec Security Associations

Now actual data traffic can flow securely.

---

## Task

- Make a load balancer
- VMSS
- deploy Organic blabla application using bootstrap
- Configure the autosacling rules to set min, max based on the matrix
- To monitor the traffic, make azure monitor 
- Whenever the traffic increases, we have to recieve notification to our email. To implement this use azure service bus. Here use subscrition model/pattern or topic based.

Phase	Task
1	Create Resource Group
2	Create VMSS
3	Create Load Balancer
4	Deploy Organic Bootstrap App
5	Configure Autoscaling
6	Configure Azure Monitor
7	Create Alert Rules
8	Create Service Bus Namespace
9	Create Topic
10	Create Topic Subscription
11	Create Logic App
12	Connect Alert → Service Bus
13	Send Email Notifications
14	Test Entire Flow


enable insights in monitoring in vmss
made alter rule
create service bus namespace - standard
-> create topic 
-> go into topic -> made subscription 
Create a logic app
