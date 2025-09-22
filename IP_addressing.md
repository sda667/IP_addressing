# IP Addressing and Static Routing  
**Author:** Ali Bahja  
**Date:** 29 November 2024  

---

## Objective
The aim of this lab is to configure **IP addressing** on multiple hosts and routers, implement **static routing**, and validate connectivity using diagnostic tools.  
The study also analyzes router behavior when forwarding packets (TTL and checksum modifications) and verifies routing decisions based on prefix matching.

---

## Topology and Setup
- Initial network: two hosts connected through a router.  
- Extended topology: additional hosts (host3 and nsa2) to test routing tables and default routes.  

(Figure: Network topology not included here.)

---

## Procedure and Results

### IP Addressing
- **Host1** configured with: `69.192.27.44/23`  
  - `/23` mask → 23 bits for network ID, 9 bits for host ID → 512 addresses.  

- **Host2** configured with: `38.17.3.42/24`  
  - `/24` mask → 256 addresses in subnet (254 usable).  

- **Collision domain B**: `17.44.46.192/26`  
  - `/26` mask → 64 addresses, 62 usable.  

To simplify routing, host addresses were reconfigured with host ID = 1:  
- host1: `69.192.26.1/23`  
- host2: `38.17.3.1/24`  

Router configuration:  
```bash
router$ ip addr add 69.192.26.1/23 dev eth0
router$ ip addr add 38.17.3.1/24 dev eth1
```

Ping tests confirmed connectivity between host1 and host2.  

---

### Traceroute Analysis
- From host1 → host2: one hop, destination `38.17.3.1`.  
- From host2 → host1: one hop, destination `69.192.26.1`.  

Traceroute results:  
- Hop count: 1  
- Destination IP: `38.17.3.1`  
- RTT values: 1 ms, 1 ms, 0 ms  

This confirms symmetrical direct paths between hosts.  

---

### Static Routing
From nsa2 → host3: no reply (host3 missing default route).  

Fixed by adding:  
```bash
host3$ ip route add default via 17.44.46.129 proto static
```

After this, host3 successfully replies to pings from nsa2.  

---

### Router Behavior
When a datagram passes through the router:  
- **TTL** decreases by 1 (64 → 63).  
- **Checksum** recalculated due to TTL change.  

Router forwards packets based on **longest prefix match** in the routing table.  

---

### Routing Table Completion
Router R routing table was completed by associating each interface with its collision domain.  

(Figure: Routing table of router R not included here.)  

Configured with:  
```bash
ip route add <IP/MASK> via <NEXT-HOP> proto static
```

No default route required, since explicit routes covered all reachable networks.  

---

## Conclusion
This lab demonstrates:  
- Configuring IP addressing and subnet analysis.  
- Implementing static routing and validating connectivity with traceroute.  
- Router behavior (TTL decrement, checksum recalculation).  
- Completion of routing table for full communication without default routes.  
