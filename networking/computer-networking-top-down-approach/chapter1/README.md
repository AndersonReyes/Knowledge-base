# Chapter 1: Computer Networks and the Internet

**Source:** Computer Networking: A Top-Down Approach, 8th ed. — Kurose & Ross  
**Pages:** 13–92

## Sections

| File | Section | Topics |
|------|---------|--------|
| [1.1-what-is-the-internet.md](1.1-what-is-the-internet.md) | 1.1 What Is the Internet? | Nuts-and-bolts, services view, protocols, RFCs, IETF |
| [1.2-the-network-edge.md](1.2-the-network-edge.md) | 1.2 The Network Edge | Access networks (DSL, cable, FTTH, 5G, Ethernet, WiFi), physical media |
| [1.3-the-network-core.md](1.3-the-network-core.md) | 1.3 The Network Core | Packet switching, circuit switching, FDM/TDM, ISP hierarchy |
| [1.4-delay-loss-throughput.md](1.4-delay-loss-throughput.md) | 1.4 Delay, Loss, and Throughput | Processing/queuing/transmission/propagation delays, traffic intensity, bottleneck links |
| [1.5-protocol-layers.md](1.5-protocol-layers.md) | 1.5 Protocol Layers and Their Service Models | 5-layer Internet stack, encapsulation, PDU names |
| [1.6-networks-under-attack.md](1.6-networks-under-attack.md) | 1.6 Networks Under Attack | Malware/botnets, DoS/DDoS, packet sniffing, IP spoofing |
| [1.7-history.md](1.7-history.md) | 1.7 History | ARPAnet → TCP/IP → Web → modern Internet timeline |

## Key Formulas Quick Reference

| Formula | Meaning |
|---------|---------|
| `d_trans = L/R` | Transmission delay: L bits at rate R |
| `d_prop = d/s` | Propagation delay: distance d, speed s |
| `d_nodal = d_proc + d_queue + d_trans + d_prop` | Total nodal delay |
| `d_end-end = N(d_proc + d_trans + d_prop)` | End-to-end delay, N links, uncongested |
| `traffic intensity = La/R` | L bits/pkt, a pkts/s arrival rate, R link rate |
| `throughput = min{R1, R2, ..., RN}` | Bottleneck link determines throughput |
| `store-and-forward: N links → NL/R` | End-to-end delay with N routers |

## Internet Stack at a Glance

```
Application   — HTTP, SMTP, FTP, DNS         [message]
Transport     — TCP, UDP                      [segment]
Network       — IP + routing protocols        [datagram]
Link          — Ethernet, WiFi, DOCSIS        [frame]
Physical      — bits on wire/fiber/radio      [bit]
```
