# Chapter 3: Transport Layer

**Source**: Computer Networking: A Top-Down Approach, 8th ed. (Kurose & Ross)  
**Pages**: 182–282 (epub p0193–p0295)

## Sections

| Section | Title | File | Key Topics |
|---------|-------|------|------------|
| 3.1 | Introduction and Transport-Layer Services | [3.1-introduction.md](3.1-introduction.md) | Logical communication, UDP vs TCP overview, IP service model |
| 3.2 | Multiplexing and Demultiplexing | [3.2-multiplexing-demultiplexing.md](3.2-multiplexing-demultiplexing.md) | Port numbers, UDP 2-tuple, TCP 4-tuple, welcoming socket |
| 3.3 | Connectionless Transport: UDP | [3.3-udp.md](3.3-udp.md) | UDP segment structure, checksum, why use UDP |
| 3.4 | Principles of Reliable Data Transfer | [3.4-reliable-data-transfer.md](3.4-reliable-data-transfer.md) | rdt1.0–3.0, ARQ, pipelining, GBN, Selective Repeat |
| 3.5 | Connection-Oriented Transport: TCP | [3.5-tcp.md](3.5-tcp.md) | Segment structure, seq/ACK numbers, RTT estimation, reliable transfer, flow control, connection management |
| 3.6 | Principles of Congestion Control | [3.6-congestion-control-principles.md](3.6-congestion-control-principles.md) | Congestion costs, scenarios, end-to-end vs network-assisted |
| 3.7 | TCP Congestion Control | [3.7-tcp-congestion-control.md](3.7-tcp-congestion-control.md) | Slow start, congestion avoidance, fast recovery, AIMD, CUBIC, ECN, Vegas, BBR, QUIC, fairness |

## Key Formulas

```
# RTT Estimation
EstimatedRTT = (1 − α) × EstimatedRTT + α × SampleRTT    [α = 0.125]
DevRTT       = (1 − β) × DevRTT + β × |SampleRTT − EstimatedRTT|  [β = 0.25]
TimeoutInterval = EstimatedRTT + 4 × DevRTT

# Stop-and-wait utilization
U_sender = (L/R) / (RTT + L/R)

# TCP send rate (approx)
rate ≈ cwnd / RTT  bytes/sec

# TCP Reno average throughput
avg_throughput = 0.75 × W / RTT   (W = cwnd at loss)

# TCP effective send constraint
LastByteSent − LastByteAcked ≤ min(cwnd, rwnd)
```

## Critical Concepts at a Glance

- **Transport layer**: process-to-process; network layer: host-to-host
- **UDP**: connectionless, unreliable, 8-byte header; TCP: connection-oriented, reliable, 20-byte header
- **Demux key**: UDP uses (dst IP, dst port); TCP uses (src IP, src port, dst IP, dst port)
- **ARQ mechanisms**: checksum + ACK/NAK + retransmission + sequence numbers
- **GBN**: cumulative ACK, no receiver buffering, retransmit entire window on loss
- **SR**: selective ACK, receiver buffers out-of-order, retransmit only lost packet; max window = 2^(k-1)
- **TCP reliability**: cumulative ACKs, single timer (oldest unACKed), fast retransmit on 3 dup ACKs
- **Flow control**: receiver advertises rwnd; sender constrained by min(cwnd, rwnd)
- **Congestion control**: slow start (exponential) → congestion avoidance (linear/AIMD) → fast recovery
- **TCP CUBIC**: cubic function of time since last loss; default in Linux; ~50% of top web servers
- **ECN**: 2 IP bits; router marks → receiver echoes → sender reduces cwnd
- **QUIC**: reliability + congestion control + encryption over UDP; used in HTTP/3
