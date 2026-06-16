# Chapter 4: Network Layer — Data Plane

**Source**: Kurose & Ross, *Computer Networking: A Top-Down Approach*, 8th ed.
**Pages**: 304–363 (epub p0315–p0388)

## Chapter Index

| Section | File | Topics |
|---|---|---|
| 4.1 | [4.1-network-layer-overview.md](4.1-network-layer-overview.md) | Forwarding vs. routing, data/control plane, traditional vs. SDN control, network service model, best-effort |
| 4.2 | [4.2-whats-inside-a-router.md](4.2-whats-inside-a-router.md) | Router architecture, input port processing, longest prefix match, TCAM, switching fabrics (memory/bus/crossbar), HOL blocking, output queuing, AQM, bufferbloat, FIFO/priority/RR/WFQ scheduling |
| 4.3 | [4.3-ip.md](4.3-ip.md) | IPv4 datagram format, IPv4 addressing, subnets, CIDR, route aggregation, DHCP, NAT, IPv6 format, IPv4→IPv6 tunneling |
| 4.4 | [4.4-generalized-forwarding-sdn.md](4.4-generalized-forwarding-sdn.md) | Match-plus-action paradigm, OpenFlow flow tables, 12 matchable fields, actions (forward/drop/modify), worked examples (forwarding/load balancing/firewall), P4 |
| 4.5 | [4.5-middleboxes.md](4.5-middleboxes.md) | Middlebox categories (NAT/security/performance), NFV, IP hourglass/narrow waist, end-to-end argument, architectural principles |

## Key Concepts Quick Reference

### Forwarding vs. Routing
- **Forwarding** = local, per-router, nanoseconds, hardware
- **Routing** = network-wide, seconds, software

### IPv4 Header Key Fields
- TTL: prevents routing loops (decremented each hop, drop at 0)
- Protocol: 6=TCP, 17=UDP (binds network→transport)
- Total length: 16 bits, max 65,535 bytes
- Typical datagram ≤ 1,500 bytes (Ethernet MTU)
- Header checksum: recomputed every hop (TTL changes)

### CIDR Notation
```
a.b.c.d/x   → x-bit prefix = network, (32-x) bits = host
```

### Longest Prefix Match
When multiple forwarding table entries match, use the one with the most specific (longest) prefix.

### Switching Fabric Comparison
| Type | Parallel? | Limit |
|---|---|---|
| Memory | No | B/2 (memory bandwidth) |
| Bus | No | Bus speed |
| Crossbar | Yes (different output ports) | N/A if outputs differ |

### NAT Translation Table
Maps `(public IP, public port) ↔ (private IP, private port)`.
16-bit port field → >60,000 simultaneous connections per public IP.

### IPv4 vs. IPv6 Header
| | IPv4 | IPv6 |
|---|---|---|
| Address | 32-bit | 128-bit |
| Header | 20 bytes min (variable) | 40 bytes fixed |
| Fragmentation | At routers | Source only |
| Checksum | Yes | Removed |
| Options | In header | Extension headers |

### OpenFlow Match Fields (1.0)
12 fields across L2 (src/dst MAC, VLAN, Ethernet type), L3 (src/dst IP, protocol, TOS), L4 (src/dst port), + ingress port.

### WFQ Bandwidth Guarantee
Class i with weight w_i gets at least:
```
R × w_i / (Σ w_j)   [R = link rate, sum over queued classes]
```

### Buffer Sizing
- Old rule: `B = RTT × C`
- Modern (many flows): `B = RTT × C / √N`
