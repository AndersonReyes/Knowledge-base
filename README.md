# Knowledge Base

A personal knowledge base for technical notes, textbook summaries, and deep-dives.

---

## Networking

### Introduction to Computer Networking

| Topic | Source | File |
|-------|--------|------|
| What Is the Internet — nuts-and-bolts, protocol definition, services view | Kurose & Ross Ch. 1.1 | [notes](networking/computer-networking-top-down-approach/chapter1/1.1-what-is-the-internet.md) |
| The Network Edge — DSL, HFC, FTTH, physical media | Kurose & Ross Ch. 1.2 | [notes](networking/computer-networking-top-down-approach/chapter1/1.2-the-network-edge.md) |
| The Network Core — packet/circuit switching, ISP hierarchy | Kurose & Ross Ch. 1.3 | [notes](networking/computer-networking-top-down-approach/chapter1/1.3-the-network-core.md) |
| Delay, Loss & Throughput — queuing delay, traffic intensity, Traceroute | Kurose & Ross Ch. 1.4 | [notes](networking/computer-networking-top-down-approach/chapter1/1.4-delay-loss-throughput.md) |
| Protocol Layers — 5-layer Internet stack, OSI model, encapsulation | Kurose & Ross Ch. 1.5 | [notes](networking/computer-networking-top-down-approach/chapter1/1.5-protocol-layers.md) |
| Networks Under Attack — malware, DoS/DDoS, sniffing, spoofing | Kurose & Ross Ch. 1.6 | [notes](networking/computer-networking-top-down-approach/chapter1/1.6-networks-under-attack.md) |
| History of Computer Networking — ARPAnet to modern Internet | Kurose & Ross Ch. 1.7 | [notes](networking/computer-networking-top-down-approach/chapter1/1.7-history.md) |

### Application Layer

| Topic | Source | File |
|-------|--------|------|
| Principles of Network Applications — architectures, sockets, TCP vs UDP | Kurose & Ross Ch. 2.1 | [notes](networking/computer-networking-top-down-approach/chapter2/2.1-principles-of-network-applications.md) |
| The Web and HTTP — HTTP/1.1, HTTP/2, caching, cookies, DASH | Kurose & Ross Ch. 2.2 | [notes](networking/computer-networking-top-down-approach/chapter2/2.2-the-web-and-http.md) |
| Electronic Mail — SMTP, POP3, IMAP | Kurose & Ross Ch. 2.3 | [notes](networking/computer-networking-top-down-approach/chapter2/2.3-electronic-mail.md) |
| DNS — hierarchy, record types, resolution, caching | Kurose & Ross Ch. 2.4 | [notes](networking/computer-networking-top-down-approach/chapter2/2.4-dns.md) |
| Video Streaming & CDN — DASH, CDN architecture, Netflix, YouTube | Kurose & Ross Ch. 2.6 | [notes](networking/computer-networking-top-down-approach/chapter2/2.6-video-streaming-and-cdn.md) |
| Socket Programming — UDP/TCP in Python | Kurose & Ross Ch. 2.7 | [notes](networking/computer-networking-top-down-approach/chapter2/2.7-socket-programming.md) |

| P2P Distributed File Sharing — DHT, Kademlia, Chord, BitTorrent internals, NAT traversal, IPFS | Kurose & Ross Ch. 2.5 + deep-dive (BEPs, RFCs, papers) | [notes](networking/computer-networking-top-down-approach/chapter2/2.5-peer-to-peer-distributed-file-sharing.md) |

### Transport Layer

| Topic | Source | File |
|-------|--------|------|
| Introduction — transport vs. network layer, TCP/UDP overview | Kurose & Ross Ch. 3.1 | [notes](networking/computer-networking-top-down-approach/chapter3/3.1-introduction.md) |
| Multiplexing & Demultiplexing — port numbers, 2-tuple vs 4-tuple | Kurose & Ross Ch. 3.2 | [notes](networking/computer-networking-top-down-approach/chapter3/3.2-multiplexing-demultiplexing.md) |
| UDP — segment structure, checksum, use cases | Kurose & Ross Ch. 3.3 | [notes](networking/computer-networking-top-down-approach/chapter3/3.3-udp.md) |
| Reliable Data Transfer — rdt FSMs, pipelining, GBN, SR | Kurose & Ross Ch. 3.4 | [notes](networking/computer-networking-top-down-approach/chapter3/3.4-reliable-data-transfer.md) |
| TCP — segment header, RTT estimation, flow control, 3-way handshake | Kurose & Ross Ch. 3.5 | [notes](networking/computer-networking-top-down-approach/chapter3/3.5-tcp.md) |
| Congestion Control Principles — scenarios, end-to-end vs network-assisted | Kurose & Ross Ch. 3.6 | [notes](networking/computer-networking-top-down-approach/chapter3/3.6-congestion-control-principles.md) |
| TCP Congestion Control — slow start, AIMD, CUBIC, ECN, BBR, QUIC | Kurose & Ross Ch. 3.7 | [notes](networking/computer-networking-top-down-approach/chapter3/3.7-tcp-congestion-control.md) |

### Network Layer: Data Plane

| Topic | Source | File |
|-------|--------|------|
| Network Layer Overview — forwarding vs. routing, data/control plane | Kurose & Ross Ch. 4.1 | [notes](networking/computer-networking-top-down-approach/chapter4/4.1-network-layer-overview.md) |
| What's Inside a Router — input/output ports, switching fabric, scheduling | Kurose & Ross Ch. 4.2 | [notes](networking/computer-networking-top-down-approach/chapter4/4.2-whats-inside-a-router.md) |
| IP — IPv4 header, CIDR, DHCP, NAT, IPv6, tunneling | Kurose & Ross Ch. 4.3 | [notes](networking/computer-networking-top-down-approach/chapter4/4.3-ip.md) |
| Generalized Forwarding & SDN — OpenFlow flow tables, P4 | Kurose & Ross Ch. 4.4 | [notes](networking/computer-networking-top-down-approach/chapter4/4.4-generalized-forwarding-sdn.md) |
| Middleboxes — NAT, firewalls, NFV, end-to-end argument | Kurose & Ross Ch. 4.5 | [notes](networking/computer-networking-top-down-approach/chapter4/4.5-middleboxes.md) |

### Network Layer: Control Plane

| Topic | Source | File |
|-------|--------|------|
| Introduction — per-router vs. logically centralized control | Kurose & Ross Ch. 5.1 | [notes](networking/computer-networking-top-down-approach/chapter5/5.1-introduction.md) |
| Routing Algorithms — Dijkstra (LS), Bellman-Ford (DV), count-to-infinity | Kurose & Ross Ch. 5.2 | [notes](networking/computer-networking-top-down-approach/chapter5/5.2-routing-algorithms.md) |
| Intra-AS Routing: OSPF — areas, flooding, authentication, MOSPF | Kurose & Ross Ch. 5.3 | [notes](networking/computer-networking-top-down-approach/chapter5/5.3-intra-as-routing-ospf.md) |
| BGP — eBGP/iBGP, AS-PATH, hot potato routing, policy, IP-anycast | Kurose & Ross Ch. 5.4 | [notes](networking/computer-networking-top-down-approach/chapter5/5.4-bgp.md) |
| SDN Control Plane — controller architecture, OpenFlow, ODL, ONOS, Google B4 | Kurose & Ross Ch. 5.5 | [notes](networking/computer-networking-top-down-approach/chapter5/5.5-sdn-control-plane.md) |
| ICMP — message types, ping, traceroute | Kurose & Ross Ch. 5.6 | [notes](networking/computer-networking-top-down-approach/chapter5/5.6-icmp.md) |
| Network Management — SNMP/MIB, NETCONF/YANG, CLI | Kurose & Ross Ch. 5.7 | [notes](networking/computer-networking-top-down-approach/chapter5/5.7-network-management.md) |

### Link Layer and LANs

| Topic | Source | File |
|-------|--------|------|
| Introduction — link-layer services, NIC architecture | Kurose & Ross Ch. 6.1 | [notes](networking/computer-networking-top-down-approach/chapter6/6.1-introduction.md) |
| Error Detection & Correction — parity, CRC, Internet checksum | Kurose & Ross Ch. 6.2 | [notes](networking/computer-networking-top-down-approach/chapter6/6.2-error-detection-correction.md) |
| Multiple Access Protocols — ALOHA, CSMA/CD, polling, DOCSIS | Kurose & Ross Ch. 6.3 | [notes](networking/computer-networking-top-down-approach/chapter6/6.3-multiple-access-protocols.md) |
| LANs — Ethernet, ARP, switches (self-learning), VLANs (802.1Q) | Kurose & Ross Ch. 6.4 | [notes](networking/computer-networking-top-down-approach/chapter6/6.4-lans-ethernet-arp-vlans.md) |
| MPLS — label switching, traffic engineering, VPN support | Kurose & Ross Ch. 6.5 | [notes](networking/computer-networking-top-down-approach/chapter6/6.5-mpls.md) |
| Data Center Networking — Clos topology, ECMP, RDMA, load balancing | Kurose & Ross Ch. 6.6 | [notes](networking/computer-networking-top-down-approach/chapter6/6.6-data-center-networking.md) |
| Day in the Life of a Web Request — DHCP→ARP→DNS→TCP→HTTP end-to-end | Kurose & Ross Ch. 6.7 | [notes](networking/computer-networking-top-down-approach/chapter6/6.7-day-in-the-life.md) |

### Wireless and Mobile Networks

| Topic | Source | File |
|-------|--------|------|
| Introduction — wireless taxonomy, infrastructure vs. ad hoc | Kurose & Ross Ch. 7.1 | [notes](networking/computer-networking-top-down-approach/chapter7/7.1-introduction.md) |
| Wireless Links — path loss, SNR/BER, hidden terminal, CDMA | Kurose & Ross Ch. 7.2 | [notes](networking/computer-networking-top-down-approach/chapter7/7.2-wireless-links.md) |
| WiFi: 802.11 — standards table, CSMA/CA, RTS/CTS, frame format, power management | Kurose & Ross Ch. 7.3 | [notes](networking/computer-networking-top-down-approach/chapter7/7.3-wifi-802-11.md) |
| Cellular Networks: 4G & 5G — LTE architecture, OFDM, 5G NR, eMBB/URLLC/mMTC | Kurose & Ross Ch. 7.4 | [notes](networking/computer-networking-top-down-approach/chapter7/7.4-cellular-4g-5g.md) |
| Mobility Principles — home/visited networks, indirect vs. direct routing | Kurose & Ross Ch. 7.5 | [notes](networking/computer-networking-top-down-approach/chapter7/7.5-mobility-principles.md) |
| Mobile IP & 4G Handover — GTP tunnels, handover sequence | Kurose & Ross Ch. 7.6 | [notes](networking/computer-networking-top-down-approach/chapter7/7.6-mobile-ip.md) |
| Mobility in Cellular — control/data plane roles, entity table | Kurose & Ross Ch. 7.7 | [notes](networking/computer-networking-top-down-approach/chapter7/7.7-mobility-in-cellular.md) |
| Impact on Higher-Layer Protocols — TCP over wireless, location-aware apps | Kurose & Ross Ch. 7.8 | [notes](networking/computer-networking-top-down-approach/chapter7/7.8-impact-on-higher-layers.md) |

### Security in Computer Networks

| Topic | Source | File |
|-------|--------|------|
| What Is Network Security — CIA properties, threat model | Kurose & Ross Ch. 8.1 | [notes](networking/computer-networking-top-down-approach/chapter8/8.1-what-is-network-security.md) |
| Cryptography — block ciphers, CBC, RSA, AES, session keys | Kurose & Ross Ch. 8.2 | [notes](networking/computer-networking-top-down-approach/chapter8/8.2-cryptography.md) |
| Message Integrity & Digital Signatures — HMAC, RSA signatures, X.509 certs | Kurose & Ross Ch. 8.3 | [notes](networking/computer-networking-top-down-approach/chapter8/8.3-message-integrity-digital-signatures.md) |
| End-Point Authentication — nonce challenge-response, ap1.0–ap4.0 | Kurose & Ross Ch. 8.4 | [notes](networking/computer-networking-top-down-approach/chapter8/8.4-endpoint-authentication.md) |
| Securing Email — PGP, hybrid encryption, sender authentication | Kurose & Ross Ch. 8.5 | [notes](networking/computer-networking-top-down-approach/chapter8/8.5-securing-email.md) |
| TLS — handshake, 4-key derivation, record format, QUIC | Kurose & Ross Ch. 8.6 | [notes](networking/computer-networking-top-down-approach/chapter8/8.6-tls.md) |
| IPsec & VPNs — AH vs ESP, SA, IKE, tunnel mode | Kurose & Ross Ch. 8.7 | [notes](networking/computer-networking-top-down-approach/chapter8/8.7-ipsec-vpn.md) |
| Securing Wireless — WPA2/WPA3, EAP/RADIUS, 4G AKA | Kurose & Ross Ch. 8.8 | [notes](networking/computer-networking-top-down-approach/chapter8/8.8-securing-wireless.md) |
| Firewalls & IDS — packet filter, stateful, application gateway, Snort | Kurose & Ross Ch. 8.9 | [notes](networking/computer-networking-top-down-approach/chapter8/8.9-firewalls-ids.md) |

---

## Books & References

| Book | Coverage | Index |
|------|----------|-------|
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 1 — Introduction | [chapter index](networking/computer-networking-top-down-approach/chapter1/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 2 — Application Layer | [chapter index](networking/computer-networking-top-down-approach/chapter2/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 3 — Transport Layer | [chapter index](networking/computer-networking-top-down-approach/chapter3/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 4 — Network Layer: Data Plane | [chapter index](networking/computer-networking-top-down-approach/chapter4/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 5 — Network Layer: Control Plane | [chapter index](networking/computer-networking-top-down-approach/chapter5/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 6 — Link Layer and LANs | [chapter index](networking/computer-networking-top-down-approach/chapter6/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 7 — Wireless and Mobile Networks | [chapter index](networking/computer-networking-top-down-approach/chapter7/README.md) |
| *Computer Networking: A Top-Down Approach*, Kurose & Ross (8th ed.) | Chapter 8 — Security in Computer Networks | [chapter index](networking/computer-networking-top-down-approach/chapter8/README.md) |
