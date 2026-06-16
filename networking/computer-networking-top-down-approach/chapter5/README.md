# Chapter 5: The Network Layer — Control Plane

**Source:** Kurose & Ross, *Computer Networking: A Top-Down Approach*, 8th ed.
**Pages:** 378–436 (epub pp. 389–448)

---

## Chapter Overview

The control plane is the network-wide logic that computes, maintains, and installs the forwarding/flow tables used by the data plane. Two broad approaches:

| Approach | Description | Protocols |
|---|---|---|
| **Per-router control** | Each router runs routing algorithm; components communicate with each other | OSPF, BGP, RIP |
| **Logically centralized (SDN)** | Remote controller computes tables; pushes to all routers via control agents | OpenFlow + SDN controller |

---

## Sections

| File | Section | Key Topics | Pages |
|---|---|---|---|
| [5.1-introduction.md](5.1-introduction.md) | 5.1 Introduction | Per-router vs. logically centralized control; SDN adoption | 378–380 |
| [5.2-routing-algorithms.md](5.2-routing-algorithms.md) | 5.2 Routing Algorithms | Link-state (Dijkstra), Distance-vector (Bellman-Ford), count-to-infinity, poisoned reverse | 381–395 |
| [5.3-intra-as-routing-ospf.md](5.3-intra-as-routing-ospf.md) | 5.3 Intra-AS Routing: OSPF | Autonomous systems, ASNs, OSPF features, hierarchy/areas | 395–399 |
| [5.4-bgp.md](5.4-bgp.md) | 5.4 BGP | eBGP/iBGP, AS-PATH/NEXT-HOP/LOCAL-PREF, hot potato routing, route selection, IP-anycast, routing policy | 399–410 |
| [5.5-sdn-control-plane.md](5.5-sdn-control-plane.md) | 5.5 SDN Control Plane | SDN architecture, OpenFlow protocol, controller layers, ODL, ONOS, Google B4 | 411–422 |
| [5.6-icmp.md](5.6-icmp.md) | 5.6 ICMP | Message types, ping, traceroute mechanism | 423–425 |
| [5.7-network-management.md](5.7-network-management.md) | 5.7 Network Management | SNMP/MIB, SNMPv3 PDU types, NETCONF operations, YANG, NETCONF examples | 425–436 |

---

## Key Algorithms

### Dijkstra's LS Algorithm (§5.2.1)
```
N' = {u}; D(neighbors) = c(u,v); D(others) = ∞
Loop: find w ∉ N' with min D(w); add to N'; update D(v) = min(D(v), D(w)+c(w,v))
Until N' = N
Complexity: O(n²) naive; O(n log n) with heap
```

### Bellman-Ford DV Equation (§5.2.2)
```
dx(y) = min_v { c(x,v) + dv(y) }   for all neighbors v of x
```

### BGP Route Selection Order (§5.4.3)
1. Highest LOCAL-PREF (policy)
2. Shortest AS-PATH
3. Hot potato (lowest intra-AS cost to NEXT-HOP)
4. BGP identifier tiebreak

---

## Critical Concepts at a Glance

- **LS vs. DV:** LS = global info, faster convergence, no count-to-infinity; DV = local info, simpler messages, count-to-infinity problem
- **OSPF areas:** Each area runs its own LS; backbone area (Area 0) routes between areas
- **eBGP vs. iBGP:** eBGP crosses AS boundaries (TCP 179); iBGP is full-mesh within AS
- **SDN southbound/northbound:** Southbound = OpenFlow (controller↔switches); Northbound = REST/intent API (controller↔apps)
- **ICMP traceroute:** TTL-expiry generates type 11; port-unreachable type 3 code 3 signals arrival at destination
- **SNMP transport:** UDP; request-response + traps; NETCONF uses TLS/TCP + XML + atomic multi-device transactions
