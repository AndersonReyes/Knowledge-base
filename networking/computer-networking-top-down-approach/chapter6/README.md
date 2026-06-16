# Chapter 6: The Link Layer and LANs

**Kurose & Ross — Computer Networking: A Top-Down Approach, 8th ed.**

## Sections

| File | Section | Topics |
|------|---------|--------|
| [6.1-introduction.md](6.1-introduction.md) | 6.1 | Node/link terminology, link-layer services, NIC architecture |
| [6.2-error-detection-correction.md](6.2-error-detection-correction.md) | 6.2 | Parity, checksums, CRC |
| [6.3-multiple-access-protocols.md](6.3-multiple-access-protocols.md) | 6.3 | Channel partitioning, ALOHA, CSMA/CD, taking-turns, DOCSIS |
| [6.4-lans-ethernet-arp-vlans.md](6.4-lans-ethernet-arp-vlans.md) | 6.4 | MAC addresses, ARP, Ethernet frame, switches, VLANs |
| [6.5-mpls.md](6.5-mpls.md) | 6.5 | MPLS label switching, traffic engineering, VPNs |
| [6.6-data-center-networking.md](6.6-data-center-networking.md) | 6.6 | DC architecture, load balancing, Clos networks, trends |
| [6.7-day-in-the-life.md](6.7-day-in-the-life.md) | 6.7 | End-to-end protocol walkthrough: DHCP→ARP→DNS→TCP→HTTP |

## Key Concepts at a Glance

- **Node**: any device running a link-layer protocol (host, router, switch, AP)
- **Link**: communication channel connecting adjacent nodes
- **Frame**: link-layer PDU encapsulating a network-layer datagram
- **NIC (network adapter)**: implements link layer in hardware
- **MAC address**: 6-byte flat address, IEEE-managed, burned into adapter
- **ARP**: resolves IP→MAC within a subnet (RFC 826)
- **Ethernet**: dominant wired LAN; CSMA/CD, connectionless, unreliable
- **Switch**: self-learning, store-and-forward layer-2 device; no MAC address on switch ports facing hosts
- **VLAN**: logical broadcast domain isolation on shared physical switch
- **MPLS**: label-switched forwarding between IP routers; enables TE and fast reroute
- **CRC-32**: `G = 100000100110000010001110110110111`; detects all burst errors ≤ r bits
- **Slotted ALOHA max efficiency**: 1/e ≈ 37%
- **Pure ALOHA max efficiency**: 1/(2e) ≈ 18%
- **CSMA/CD efficiency**: `1 / (1 + 5·d_prop/d_trans)`
