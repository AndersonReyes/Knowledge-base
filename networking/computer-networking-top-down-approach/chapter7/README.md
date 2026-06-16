# Chapter 7: Wireless and Mobile Networks

*Kurose & Ross — Computer Networking: A Top-Down Approach, 8th ed.*

## Chapter Index

| Section | File | Key Topics |
|---|---|---|
| 7.1 Introduction | [7.1-introduction.md](7.1-introduction.md) | Wireless network elements, infrastructure vs. ad hoc, 4-type taxonomy (single/multi-hop × infra/no-infra) |
| 7.2 Wireless Links | [7.2-wireless-links.md](7.2-wireless-links.md) | Path loss, multipath, interference, SNR, BER, modulation tradeoffs, hidden terminal, CDMA encoding/decoding |
| 7.3 WiFi: 802.11 | [7.3-wifi-802-11.md](7.3-wifi-802-11.md) | 802.11 standards table, BSS/AP architecture, channels (1/6/11), CSMA/CA, RTS/CTS, frame format (4 addresses), mobility within subnet, rate adaptation, power management, Bluetooth/piconet |
| 7.4 Cellular: 4G and 5G | [7.4-cellular-4g-5g.md](7.4-cellular-4g-5g.md) | 4G LTE architecture (UE, eNode-B, MME, HSS, S-GW, P-GW), IMSI/SIM, LTE link-layer sublayers (PDCP/RLC/MAC), OFDM radio access, GTP tunnels, network attachment phases, sleep modes, global carrier network, 5G standards (eMBB/URLLC/mMTC), FR1/FR2, mmWave, 5G core (AMF/SMF/UPF) |
| 7.5 Mobility Principles | [7.5-mobility-principles.md](7.5-mobility-principles.md) | Mobility spectrum (4 scenarios), home/visited networks, HSS role, three routing approaches (BGP-based, indirect, direct), triangle routing problem |
| 7.6 Mobile IP | [7.6-mobile-ip.md](7.6-mobile-ip.md) | 4G/5G mobility steps (association → control config → tunnel setup → handover), GTP/TEID, handover sequence (7 steps), Mobile IP RFC 5944 (home/foreign agents, care-of-address, agent discovery, indirect routing) |
| 7.7 Mobility in Cellular | [7.7-mobility-in-cellular.md](7.7-mobility-in-cellular.md) | Summary diagrams: control/data plane mobility roles, roaming architecture, handover entity responsibilities |
| 7.8 Impact on Higher Layers | [7.8-impact-on-higher-layers.md](7.8-impact-on-higher-layers.md) | TCP over wireless (problem + 3 fixes: local recovery, sender awareness, split-connection), application-layer bandwidth scarcity, location-aware apps |

## Key Architectural Concepts

- **Two orthogonal problems:** wireless (link characteristics) vs. mobility (changing point of attachment)
- **4G LTE data path:** Mobile ↔ BS (wireless) ↔ S-GW (GTP) ↔ P-GW (GTP) ↔ Internet
- **Indirect routing** (4G + Mobile IP): correspondent → home network → visited network (triangle routing trade-off)
- **Handover:** BS-initiated; target BS pre-allocates resources; S-GW tunnel endpoint flips; P-GW untouched
- **CSMA/CA vs CSMA/CD:** 802.11 avoids collisions (no detection); Ethernet detects and aborts
- **5G decomposition:** MME → AMF + SMF; full NFV/CUPS; network slicing

## Quick Reference: 802.11 Non-Overlapping Channels
- 2.4 GHz band: **channels 1, 6, 11** (only non-overlapping set of 3)
- 5 GHz band: more non-overlapping channels available

## Quick Reference: LTE Link-Layer Sublayers
```
IP → PDCP (compression + encryption) → RLC (fragmentation + ARQ) → MAC (scheduling + FEC) → Physical
```

## Quick Reference: 5G Capacity Formula
```
capacity = cell_density × available_spectrum × spectral_efficiency
```
