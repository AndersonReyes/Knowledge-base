# Peer-to-Peer Distributed File Sharing

## Table of Contents

1. [Textbook Reference: Kurose & Ross — Chapter 2.5 (8th Ed.)](#1-textbook-reference-kurose--ross--chapter-25-8th-ed)
2. [Network Topology: Structured vs Unstructured Overlays](#2-network-topology-structured-vs-unstructured-overlays)
3. [Distributed Hash Tables](#3-distributed-hash-tables)
   - [Chord](#31-chord)
   - [Kademlia](#32-kademlia)
4. [Content Addressing & Data Integrity](#4-content-addressing--data-integrity)
5. [BitTorrent Protocol Internals](#5-bittorrent-protocol-internals)
6. [NAT Traversal](#6-nat-traversal)
7. [IPFS Architecture](#7-ipfs-architecture)

---

## 1. Textbook Reference: Kurose & Ross — Chapter 2.5 (8th Ed.)

> Kurose, J. & Ross, K. (2022). *Computer Networking: A Top-Down Approach* (8th ed.), Section 2.5.

### 1.1 Client-Server vs P2P Distribution Time

A clean model for comparing architectures. Assumptions: abundant core bandwidth, all bottlenecks in access links, peers dedicate full bandwidth to the transfer.

**Variables**:
- `F` — file size (bits)
- `N` — number of peers
- `u_s` — server upload rate
- `u_i` — upload rate of peer `i`
- `d_min` — lowest download rate among all peers

**Client-Server lower bound**:

```
D_cs = max(NF/u_s, F/d_min)
```

- `NF/u_s`: server must send the full file N times; only the server uploads.
- `F/d_min`: slowest peer determines the floor.
- For large N, `NF/u_s` dominates → **linear growth with N**. Double the peers, double the time. Unbounded.

**P2P lower bound**:

```
D_p2p = max(F/u_s, F/d_min, NF/(u_s + Σu_i))
```

- `F/u_s`: server must seed at least one copy into the network.
- `F/d_min`: same floor as above.
- `NF/(u_s + Σu_i)`: total bits to deliver is `NF`; total system upload capacity is `u_s + Σu_i`. Each peer that joins adds to `Σu_i`, growing the denominator.

**Key result**: As N grows, both the numerator (`NF`) and denominator (`u_s + Σu_i`) grow together. `D_p2p` stays bounded — empirically below 1 hour for any N when peers have symmetric bandwidth. P2P is *self-scaling*: every new consumer is also a new contributor.

### 1.2 BitTorrent Mechanics (Textbook Treatment)

**Bootstrap**:
1. New peer registers with the **tracker** (infrastructure node tracking active peers).
2. Tracker returns a random subset (~50 peers).
3. New peer attempts TCP connections to all 50 → successful connections = **neighboring peers**.
4. Neighbors fluctuate as peers join/leave; connections are maintained dynamically.

**Chunk exchange**:
- File split into equal-size chunks (256 KB typical).
- Periodically, each peer requests the chunk list from all neighbors.
- Peer has no chunks initially; accumulates over time.
- Once complete, peer may leave (selfish) or stay and seed (altruistic).

**Rarest first**:
- From chunks the peer doesn't have, identify which are rarest among neighbors (fewest copies).
- Request those first.
- Effect: rare chunks get redistributed quickly → equalizes copy counts across the swarm → reduces catastrophic data loss if a seeder leaves.

**Tit-for-tat / Unchoking**:
- Peer measures incoming bit rate from each neighbor continuously.
- **Top 4**: unchoke the 4 neighbors sending data at the highest rate (recalculated every 10s). Reciprocate by sending chunks to them.
- **Optimistic unchoke**: every 30s, pick 1 random choked-but-interested neighbor and unchoke it without requiring reciprocation.
- All others: **choked** (receive nothing).

**Why it works**:
- Optimistic unchoke lets new peers (who have nothing to trade yet) receive chunks, giving them something to offer.
- If the probed peer starts sending back at a good rate, it earns a top-4 slot.
- Peers with compatible upload rates converge on trading with each other.
- Free-riders (download only) stay choked by everyone and get poor performance.

The textbook notes this incentive scheme (tit-for-tat) can be gamed [Liogkas 2006; Locher 2006; Piatek 2008], but the ecosystem is robust in practice because the majority of peers cooperate — if they didn't, BitTorrent would collapse to the same dynamics as a free-rider network [Saroiu 2002].

---

## 2. Network Topology: Structured vs Unstructured Overlays

P2P networks are categorized by how nodes connect and route queries.

### Unstructured Overlays

Nodes connect arbitrarily — topology is independent of content. No guarantees about where data lives.

- **Gnutella**: Flooding-based query routing. A search query is broadcast to all connected peers, who forward it further. Scales as O(n) — catastrophically bad for large networks. Later adopted a superpeer architecture (well-connected nodes act as hubs) to mitigate this.
- **Freenet**: Cache-based adaptive routing. Queries are forwarded along a path and responses return along the same path. Provides local anonymity by design. Uses location-independent keys for content identification.
- **Topology**: Both follow a small-world / power-law degree distribution — a few highly-connected hub nodes, many low-degree peers.

**Trade-off**: Simple to implement, supports privacy features, but search is inefficient and routing does not guarantee finding content even if it exists.

### Structured Overlays

Topology is deterministically constructed based on content hashes. Every node has a defined position in the overlay, and data is placed at specific nodes according to a key-to-node mapping.

**Trade-off**: Logarithmic routing complexity O(log n), guaranteed lookup (if data exists), more complex maintenance on churn.

Real-world structured overlays: Kademlia (BitTorrent DHT, IPFS), Chord (theoretical foundation), Tapestry, Pastry.

---

## 3. Distributed Hash Tables

A DHT maps keys (typically content hashes) to values (peer addresses) across a decentralized network with no central authority. Each node stores a fraction of the total keyspace.

Core operations:
- `PUT(key, value)` — store
- `GET(key)` → value — retrieve
- Node join/leave (churn handling)

### 3.1 Chord

> Stoica et al., "Chord: A Scalable Peer-to-Peer Lookup Service for Internet Applications," ACM SIGCOMM 2001. Won ACM SIGCOMM Test of Time Award 2011.

**Paper**: https://dl.acm.org/doi/10.1145/383059.383071

**Consistent Hashing on a Ring**

Nodes and keys are both hashed (SHA-1) into an m-bit circular identifier space [0, 2^m). A key `k` is owned by the first node whose ID ≥ k (its *successor*). When a node leaves, its keys transfer to its successor — only K/N keys move on average (K = total keys, N = nodes).

```
ID space: 0 ... 2^m (circular)
Node IDs: hash(IP:port)
Key IDs:  hash(content)
key → successor(key)
```

**Finger Tables**

Each node `n` maintains m finger table entries:
```
finger[i] = successor(n + 2^(i-1) mod 2^m)   for i = 1..m
```

Lookup forwards to the finger entry closest to (but not past) the target. Each hop covers at least half the remaining distance → O(log N) hops.

**Limitations**: Asymmetric routing (only clockwise), single lookup path, does not parallelize queries. Primarily a research foundation; Kademlia dominates in practice.

---

### 3.2 Kademlia

> Maymounkov & Mazières, "Kademlia: A Peer-to-Peer Information System Based on the XOR Metric," IPTPS 2002.

**Paper**: https://dl.acm.org/doi/10.5555/646334.687801  
**S/Kademlia (security hardening)**: https://telematics.tm.kit.edu/publications/Files/267/SKademlia_2007.pdf

Used by: BitTorrent Mainline DHT (BEP 5), IPFS (KAD-DHT spec), Ethereum devp2p, Kad network.

**XOR Metric**

Node IDs are 160-bit random values. Distance between two nodes:
```
dist(a, b) = a XOR b
```

Properties of XOR distance:
- Symmetric: `dist(a,b) == dist(b,a)`
- Triangle inequality holds
- Unidirectional: for any point `x` and distance `d`, there is exactly one point `y` such that `dist(x,y) == d`
- All lookups for a given key converge along the same path regardless of origin

**K-Buckets (Routing Table)**

A node maintains 160 k-buckets. Bucket `i` holds nodes whose IDs differ from the local node in bit position `i` (i.e., `2^i ≤ dist < 2^(i+1)`).

Each bucket holds at most `k` entries (k=20 in practice). Entries are `(IP, UDP port, NodeID)` triples sorted by last-seen time.

Bucket management:
- On contact with a new node: if bucket not full, add it. If full, ping the least-recently-seen node. If it responds, discard the new node. If it doesn't, replace it with the new node.
- This biases the routing table toward long-lived nodes (more stable network).

The local node has detailed knowledge of nearby ID space and exponentially sparse knowledge of distant space.

**RPCs**

```
PING          — liveness check
STORE(key, value)
FIND_NODE(id) → k closest known nodes to id
FIND_VALUE(key) → value if found, else k closest nodes to key
```

**Node Lookup Algorithm**

To find the k closest nodes to target ID `t`:
1. Pick α (typically 3) closest known nodes to `t`.
2. Send parallel async `FIND_NODE(t)` to those α nodes.
3. Each response returns up to k nodes closer to `t`.
4. Insert new nodes into a sorted candidate list, query the α closest not yet queried.
5. Repeat until no closer nodes are returned.

Converges in O(log N) rounds. The α parallelism trades bandwidth for latency.

**Key-Value Storage**

`STORE(key, value)` is sent to the k nodes closest to `key`. To retrieve, `FIND_VALUE(key)` walks the same lookup path — any of those k nodes should have it. Data is republished every 24 hours; expires after ~24h if not republished.

In BitTorrent's DHT, the "value" stored is a compact peer list (IP:port) for a given `info_hash`.

---

## 4. Content Addressing & Data Integrity

Rather than locating data by address (where it is), content addressing locates data by hash (what it is).

### Hash Functions

| Hash | Output | Used in |
|------|--------|---------|
| SHA-1 | 160-bit | BitTorrent v1, Git (default) |
| SHA-256 | 256-bit | BitTorrent v2, IPFS CIDs |
| BLAKE2/BLAKE3 | variable | IPFS (multihash), modern systems |

A single-bit change in input produces a completely different hash (avalanche effect) — tamper detection is trivially O(1) after download.

**Multihash** (used in IPFS): a self-describing hash format that prefixes the hash with the algorithm identifier and digest length, making systems hash-agnostic.

```
<varint hash fn code><varint digest size><hash bytes>
```

### Merkle Trees

Introduced by Ralph Merkle, 1979. A binary tree where:
- Leaves = hash(data block)
- Internal nodes = hash(left_child || right_child)
- Root = compact fingerprint of entire dataset

Properties:
- Verify any single block in O(log n) by providing the sibling hashes on the path to root (Merkle proof).
- Identify exactly which blocks differ between two versions by walking the tree top-down.
- Deduplication: identical content hashes to the same leaf.

Used in: BitTorrent v2 (per-piece Merkle trees), Git (tree objects), Bitcoin (transaction Merkle root in block header), IPFS (Merkle DAG).

### Merkle DAG

Generalization of Merkle tree to a directed acyclic graph. Edges are hashes of target objects — links *authenticate* what they point to. Enables:
- Arbitrary graph structures (not just trees)
- Content-addressed versioning
- Structural sharing (multiple nodes pointing to same subgraph)

Each node: `{ links: [(name, hash, size)], data: bytes }`.

IPFS uses Merkle DAG as its core data structure, extended to IPLD (InterPlanetary Linked Data) for codec-agnostic serialization (DAG-CBOR, DAG-JSON).

### BitTorrent Piece Hashing (v1 vs v2)

**v1** (.torrent `info.pieces` field):
- File split into pieces (typically 256 KB–1 MB).
- SHA-1 hash of each piece stored concatenated in `pieces` (20 bytes × n).
- After downloading a piece, client computes SHA-1 and compares. Mismatch → discard piece.

**v2** (BEP 52):
- Replaced SHA-1 with SHA-256.
- Each piece is the root of a Merkle tree where leaves are 16 KB blocks.
- `.torrent` file stores only the root hash per piece; block-level hashes are delivered by peers during download.
- Enables per-block verification without pre-downloading the full hash list.

**Ref**: https://www.bittorrent.org/beps/bep_0052.html | https://blog.libtorrent.org/2020/09/bittorrent-v2/

---

## 5. BitTorrent Protocol Internals

All formal specs: https://www.bittorrent.org/beps/bep_0000.html

### 5.1 .torrent File & Info-Hash

`.torrent` files use **Bencode** encoding:
- Integer: `i42e`
- String: `4:spam`
- List: `l4:spami42ee`
- Dict: `d3:key5:valuee` (keys lexicographically sorted)

**Info dictionary** (the thing that gets hashed):
```
{
  name: <string>
  piece length: <int>   # bytes per piece, power of 2
  pieces: <string>      # concatenated SHA-1 hashes (v1) or "" (v2)
  length: <int>         # single-file mode
  files: [...]          # multi-file mode
}
```

**Info-hash**: SHA-1 (v1) or SHA-256 (v2) of the bencoded info dict. This is the torrent's globally unique identifier, used as the DHT key.

**Magnet link**: `magnet:?xt=urn:btih:<infohash-hex>&dn=<name>&tr=<tracker-url>`

### 5.2 Peer Discovery

Three mechanisms, used in parallel:

**Tracker (BEP 3 / BEP 15)**
- HTTP tracker: GET request with `info_hash`, `peer_id`, `port`, `uploaded`, `downloaded`, `left`, `event`. Response: bencoded dict with `peers` (compact binary: 6 bytes/peer for IPv4), `interval`.
- UDP tracker (BEP 15): Binary protocol, no bencode. Connection ID → Announce → peers. Preferred due to lower overhead.

**DHT (BEP 5)**
- Mainline DHT: Kademlia over UDP, node IDs and info-hashes both 160-bit.
- `get_peers(info_hash)` → returns either peers or closer nodes.
- `announce_peer(info_hash, port, token)` → register yourself as a peer (token from prior `get_peers` response, prevents IP spoofing).
- Enables fully trackerless operation.

**Peer Exchange / PEX (BEP 11)**
- Extension under BEP 10 extension protocol, capability name `ut_pex`.
- Once connected to a peer, they exchange lists of other peers they're connected to.
- Max one PEX message per minute (batched deltas: added + dropped peers).
- Data is untrusted — validate before connecting. Faster and more current than DHT.

### 5.3 Wire Protocol (BEP 3)

**Handshake** (68 bytes):
```
\x13 + "BitTorrent protocol"   # 20 bytes
<8 reserved bytes>              # extension flags
<20-byte info-hash>
<20-byte peer-id>
```

**Message frame**: `[4-byte length][1-byte msg-id][payload]`

| ID | Name | Payload |
|----|------|---------|
| — | keep-alive | (length=0, no id) |
| 0 | choke | — |
| 1 | unchoke | — |
| 2 | interested | — |
| 3 | not interested | — |
| 4 | have | piece-index (4 bytes) |
| 5 | bitfield | bitset of owned pieces |
| 6 | request | index, begin, length (12 bytes) |
| 7 | piece | index, begin, block-data |
| 8 | cancel | index, begin, length (12 bytes) |

Block size is typically 16 KB (2^14 bytes). A piece is made of multiple blocks.

**Full spec**: https://wiki.theory.org/BitTorrentSpecification | https://www.bittorrent.org/beps/bep_0003.html

### 5.4 Piece Selection: Rarest First

Each peer tracks how many copies of each piece exist among its connected peers. Download priority:
1. **Rarest first**: Request pieces that fewest peers have. Maximizes piece diversity across the swarm, reduces load on seeders, prevents catastrophic loss if a seeder leaves.
2. **Strict priority**: Finish downloading a piece before starting another (maximize tradeable pieces).
3. **Random first piece**: For the very first piece, pick randomly — rarest-first is sub-optimal when you have nothing to trade.
4. **End-game mode**: When nearly complete, send outstanding block requests to *all* peers simultaneously. Cancel once received. Eliminates the long tail problem.

**Ref**: Bram Cohen, "Incentives Build Robustness in BitTorrent," P2P Systems Workshop, 2003.

### 5.5 Choking / Unchoking

BitTorrent uses a tit-for-tat mechanism to incentivize uploading.

**State per peer connection**: `{choked, unchoked}` × `{interested, not-interested}` (local and remote, independently).

- A peer only uploads to unchoked peers.
- **Regular unchoke** (every 10 seconds): Upload to the 4 peers that have provided the highest download rates to you over the last 20 seconds.
- **Optimistic unchoke** (every 30 seconds): Pick 1 random interested-but-choked peer, unchoke it regardless of its upload rate. Allows new peers to bootstrap (they have nothing to trade yet), and discovers better trading partners.

Result: free-riders (download only, upload nothing) get choked by everyone and receive poor performance. Contributors get reciprocated.

**Ref**: https://arpit.substack.com/p/the-choke-algorithm-that-powers-bittorrent

---

## 6. NAT Traversal

Most peers sit behind NAT. Direct inbound connections are blocked. P2P systems need techniques to punch through.

### NAT Types

| Type | Behavior | Hole Punching |
|------|----------|---------------|
| Full Cone | Any external host can send once internal host has sent to any external | Works |
| Address-Restricted | Must have sent to external IP first | Works |
| Port-Restricted | Must have sent to exact IP:port first | Works |
| Symmetric | Different external port per destination — unpredictable | Fails |

### STUN (RFC 8489, obsoletes RFC 5389)

**Spec**: https://datatracker.ietf.org/doc/html/rfc8489

Client sends a binding request to a public STUN server → server replies with the observed source IP:port (the client's external address as seen after NAT). Client learns its public address and port mapping.

STUN alone is not sufficient for symmetric NAT — the observed port is only valid for that specific STUN server destination.

### UDP Hole Punching

1. Both peers A and B connect to a rendezvous server (reports their external IP:port).
2. Server gives each peer the other's external address.
3. Both simultaneously send UDP packets to each other's external IP:port.
4. Each outbound packet creates a NAT entry permitting the inbound packet from the peer.
5. Subsequent packets pass through.

Critical: must be *simultaneous*. If one peer sends too early, the other's NAT drops it before their own outbound packet creates the mapping.

Success rate ~82% of NAT devices. Fails with symmetric NAT.

### TCP Hole Punching

Both peers initiate outbound TCP SYN to each other's external address simultaneously. NAT passes inbound SYN if a recent outbound attempt matches. Requires `SO_REUSEADDR` + `SO_REUSEPORT` to bind the same local port for both connect and accept.

Lower success rate (~64%) than UDP — TCP's stateful handshake is less forgiving.

### TURN (RFC 5766)

**Spec**: https://www.rfc-editor.org/rfc/rfc5766

When hole punching fails (symmetric NAT), use a relay. Client connects to TURN server, allocates a relay address on the server. Peers send/receive via the relay. 100% connectivity but adds latency and relay server resource cost.

### ICE (RFC 8445, obsoletes RFC 5245)

**Spec**: https://www.rfc-editor.org/info/rfc8445

Orchestration framework combining STUN + TURN. Used in WebRTC, VoIP.

1. **Candidate gathering**: local addresses, STUN-reflexive addresses, TURN relay addresses.
2. **Exchange candidates** via signaling channel (SDP offer/answer).
3. **Connectivity checks**: STUN binding requests for every candidate pair.
4. **Nominate** the highest-priority working pair (prefer direct > reflexive > relay).

ICE achieves ~95%+ connectivity by falling back through candidate types.

### NAT Traversal in BitTorrent

BEP 55 (Holepunch Extension): allows two peers behind NAT to coordinate hole punching via a third peer that is connected to both (acts as the rendezvous point). The third peer sends `rendezvous` → both peers then send `connect` to each other simultaneously via uTP (UDP).

**Ref**: https://www.bittorrent.org/beps/bep_0055.html

---

## 7. IPFS Architecture

> Juan Benet, "IPFS - Content Addressed, Versioned, P2P File System," arXiv:1407.3561, 2014.

**Whitepaper**: https://arxiv.org/abs/1407.3561  
**Docs**: https://docs.ipfs.tech  
**Specs**: https://specs.ipfs.tech

IPFS is a content-addressed P2P file system. Instead of `https://host/path`, you address content by `CID` (content identifier = hash of the data).

### 7.1 Content Identifiers (CIDs)

**Docs**: https://docs.ipfs.tech/concepts/content-addressing/

A CID encodes: codec + multihash (hash function + digest). Self-describing and hash-agnostic.

```
CIDv0: base58btc(multihash)         — legacy, SHA2-256 only
CIDv1: <version><codec><multihash>  — current
```

Example (CIDv1, base32):
```
bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi
```

### 7.2 libp2p

**Specs**: https://github.com/libp2p/specs  
**Site**: https://libp2p.io

Modular networking stack extracted from IPFS. Also used by Ethereum 2.0, Polkadot, Filecoin.

Modules:
- **Transport**: TCP, QUIC, WebSockets, WebTransport
- **Multiplexing**: yamux, mplex — multiple logical streams over one connection
- **Security**: Noise protocol, TLS 1.3
- **Peer identity**: Ed25519 key pair → PeerID = multihash(public key)
- **Discovery**: mDNS (local), KAD-DHT (global), bootstrap peers
- **NAT traversal**: AutoNAT (detect NAT type), Circuit Relay (TURN equivalent), DCUtR (hole punching coordination)

Multiaddr notation encodes a full routable address:
```
/ip4/1.2.3.4/tcp/4001/p2p/QmPeerId
/dns4/peer.example.com/tcp/443/wss/p2p/QmPeerId
```

### 7.3 KAD-DHT (Content Routing)

**Spec**: https://specs.ipfs.tech/routing/kad-dht/

Kademlia variant. Maps `CID → [PeerID]` (which peers have this content) and `PeerID → multiaddrs` (how to reach a peer).

Two DHT modes:
- **Client mode**: queries DHT but does not serve queries (firewalled nodes)
- **Server mode**: full DHT participant, stores and serves records

`FIND_PROVIDERS(CID)` — returns peers that have announced they hold the CID.  
`PROVIDE(CID)` — announce you hold a CID (republished every 12 hours, expires after 24h).

### 7.4 Bitswap

**Docs**: https://docs.ipfs.tech/concepts/bitswap/

Block exchange protocol. Generalizes BitTorrent's piece exchange to arbitrary CID-addressed blocks.

- Each node maintains a **want-list**: CIDs it needs.
- On connection to a peer, exchange want-lists.
- If peer has a wanted block, it sends it.
- **Want-have** vs **want-block**: first send `WANT_HAVE` (does peer have this?) before `WANT_BLOCK` (send me the block) — avoids wasting bandwidth.
- Sessions: group related block requests (e.g., all blocks of one file) to the same set of peers.

Unlike BitTorrent, there is no strict tit-for-tat; Bitswap uses a credit system (ledger per peer pair) but enforcement is lax in practice.

### 7.5 UnixFS

**Spec**: https://specs.ipfs.tech/data-formats/unixfs/

Data format for representing traditional filesystem objects (files, directories, symlinks) on top of IPLD/Merkle DAG.

**Chunking**: Large files are split into blocks and laid out as a Merkle DAG:
- **Fixed-size**: default 256 KB chunks
- **Rabin fingerprinting**: content-defined chunking at natural boundaries → better deduplication across similar files

Directory: a dag-pb node whose links are `(name, CID)` pairs pointing to child file/directory nodes.

### 7.6 IPNS (Mutable Naming)

**Docs**: https://docs.ipfs.tech/concepts/ipns/

CIDs are immutable — updating a file changes its CID. IPNS creates mutable pointers.

- IPNS name = hash of a public key (Ed25519 by default, per RFC 8032).
- Publish a signed record: `IPNS_name → CID` with sequence number and TTL.
- Records distributed via DHT.
- To resolve: `FIND_VALUE(IPNS_name)` → CID.
- Only the holder of the private key can publish a new record (higher sequence number wins).

### 7.7 Protocol Stack Summary

```
Application (UnixFS, IPNS)
     │
IPLD / Merkle DAG (content-addressed data model)
     │
Bitswap (block exchange)
     │
KAD-DHT (content + peer routing)
     │
libp2p (transport, security, multiplexing, peer discovery, NAT)
     │
TCP / QUIC / WebSockets
```

---

## References

### Foundational Papers

| Paper | Authors | Year | Link |
|-------|---------|------|------|
| Chord: A Scalable P2P Lookup Service | Stoica et al. | 2001 | https://dl.acm.org/doi/10.1145/383059.383071 |
| Kademlia: A P2P Information System Based on the XOR Metric | Maymounkov & Mazières | 2002 | https://dl.acm.org/doi/10.5555/646334.687801 |
| S/Kademlia: A Practicable Approach Towards Secure Key-Based Routing | Baumgart & Mies | 2007 | https://telematics.tm.kit.edu/publications/Files/267/SKademlia_2007.pdf |
| Incentives Build Robustness in BitTorrent | Bram Cohen | 2003 | https://bittorrent.org/bittorrentecon.pdf |
| IPFS - Content Addressed, Versioned, P2P File System | Juan Benet | 2014 | https://arxiv.org/abs/1407.3561 |
| IPFS and Friends: A Qualitative Comparison of P2P Data Networks | Schubotz et al. | 2021 | https://arxiv.org/abs/2102.12737 |
| Performance Modeling of BitTorrent P2P Networks | Various | 2013 | https://arxiv.org/abs/1311.1195 |
| Probabilistic Analysis of Kademlia Networks | Various | 2013 | https://arxiv.org/abs/1309.5866 |

### RFCs

| RFC | Title |
|-----|-------|
| RFC 8489 | Session Traversal Utilities for NAT (STUN) — https://datatracker.ietf.org/doc/html/rfc8489 |
| RFC 5766 | Traversal Using Relays around NAT (TURN) — https://www.rfc-editor.org/rfc/rfc5766 |
| RFC 8445 | Interactive Connectivity Establishment (ICE) — https://www.rfc-editor.org/info/rfc8445 |

### BitTorrent Enhancement Proposals (BEPs)

| BEP | Title | Link |
|-----|-------|------|
| BEP 3 | BitTorrent Protocol Specification (wire protocol, .torrent format) | https://www.bittorrent.org/beps/bep_0003.html |
| BEP 5 | DHT Protocol (Mainline DHT / Kademlia) | https://www.bittorrent.org/beps/bep_0005.html |
| BEP 10 | Extension Protocol | https://www.bittorrent.org/beps/bep_0010.html |
| BEP 11 | Peer Exchange (PEX) | https://www.bittorrent.org/beps/bep_0011.html |
| BEP 15 | UDP Tracker Protocol | https://www.bittorrent.org/beps/bep_0015.html |
| BEP 52 | BitTorrent v2 (SHA-256, Merkle trees) | https://www.bittorrent.org/beps/bep_0052.html |
| BEP 55 | Holepunch Extension | https://www.bittorrent.org/beps/bep_0055.html |

### Official Documentation

- BitTorrent specs index: https://www.bittorrent.org/beps/bep_0000.html
- Unofficial BT spec (wire protocol detail): https://wiki.theory.org/BitTorrentSpecification
- libtorrent blog (BT v2): https://blog.libtorrent.org/2020/09/bittorrent-v2/
- IPFS docs: https://docs.ipfs.tech
- IPFS specs: https://specs.ipfs.tech
- IPFS KAD-DHT spec: https://specs.ipfs.tech/routing/kad-dht/
- libp2p specs: https://github.com/libp2p/specs
- Git internals (content addressing reference): https://git-scm.com/book/en/v2/Git-Internals-Git-Objects
