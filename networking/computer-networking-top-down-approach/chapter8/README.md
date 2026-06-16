# Chapter 8: Security in Computer Networks

*Computer Networking: A Top-Down Approach, 8th ed. — Kurose & Ross*

## Chapter Index

| Section | Title | Key Topics |
|---|---|---|
| [8.1](8.1-what-is-network-security.md) | What Is Network Security? | Confidentiality, integrity, authentication, operational security; threat model (Alice/Bob/Trudy) |
| [8.2](8.2-cryptography.md) | Principles of Cryptography | Caesar/monoalphabetic/polyalphabetic ciphers; block ciphers; CBC; DES/3DES/AES; RSA; session keys |
| [8.3](8.3-message-integrity-digital-signatures.md) | Message Integrity and Digital Signatures | Cryptographic hash functions; MD5; SHA-1; MAC; HMAC; digital signatures; CA; X.509 certificates |
| [8.4](8.4-endpoint-authentication.md) | End-Point Authentication | ap1.0–ap4.0; nonce-based challenge-response; replay attacks |
| [8.5](8.5-securing-email.md) | Securing E-Mail | Hybrid encryption; sender authentication; PGP; web of trust |
| [8.6](8.6-tls.md) | Securing TCP Connections: TLS | TLS handshake; key derivation (4 keys); TLS records; HMAC; nonces; sequence numbers; truncation attack |
| [8.7](8.7-ipsec-vpn.md) | Network-Layer Security: IPsec and VPNs | VPNs; AH vs ESP; security associations (SA); SAD; SPD; IPsec datagram (enchilada); IKE phases |
| [8.8](8.8-securing-wireless.md) | Securing Wireless LANs and 4G/5G | WEP flaws; WPA2 four-way handshake; EAP/EAPoL/RADIUS; 4G AKA protocol; 5G changes |
| [8.9](8.9-firewalls-ids.md) | Operational Security: Firewalls and IDS | Packet filters; ACLs; stateful filters; application gateways; IDS vs IPS; signature-based; anomaly-based; Snort |

## Core Security Properties

| Property | Mechanism |
|---|---|
| Confidentiality | Symmetric key (AES/3DES) or hybrid (RSA + session key) |
| Message integrity | HMAC (symmetric) or digital signature (asymmetric) |
| End-point authentication | Nonce + shared key (ap4.0); certificates (TLS/IPsec) |
| Non-repudiation | Digital signatures with private key |
| Replay prevention | Nonces (per-session) + sequence numbers (per-packet) |

## Protocol Summary by Layer

| Layer | Protocol | Provides |
|---|---|---|
| Application | PGP | Email: confidentiality, auth, integrity |
| Transport | TLS | TCP: confidentiality, integrity, server auth |
| Network | IPsec (ESP) | IP: confidentiality, auth, integrity, replay prevention |
| Link | WPA2/WPA3 | 802.11: mutual auth, AES encryption |
| Cellular | 4G AKA / 5G | Mutual auth, AES encryption |

## Key Algorithm Reference

| Algorithm | Type | Key/Output Size | Used In |
|---|---|---|---|
| DES | Symmetric block | 56-bit key, 64-bit block | Legacy |
| 3DES | Symmetric block | 168-bit key, 64-bit block | TLS (legacy), IPsec |
| AES | Symmetric block | 128/192/256-bit key, 128-bit block | TLS, IPsec, WPA2, 4G/5G |
| RSA | Asymmetric | ~1024+ bit modulus | PGP, TLS, IKE |
| MD5 | Hash | 128-bit digest | HMAC, legacy |
| SHA-1 | Hash | 160-bit digest | HMAC, digital sigs |
| HMAC | MAC | variable | TLS, IPsec, OSPF, WPA2 |
| CBC | Block cipher mode | — | AES-CBC, 3DES-CBC |
| Diffie-Hellman | Key exchange | — | IKE Phase 1 |
