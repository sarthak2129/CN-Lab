# Experiment 9 — Transport Layer Simulation: UDP, TCP, and SCTP Comparison

---

## Experiment Title / Aim
Implement a transport layer simulation to demonstrate **process-to-process communication** using **UDP**, **TCP**, and **SCTP** protocols. Compare these protocols in terms of connection establishment, data transmission, and congestion control using Cisco Packet Tracer.

---

## Objective
- Understand and apply transport layer protocols: UDP, TCP, and SCTP.
- Demonstrate process-to-process communication using different transport layer protocols.
- Analyse and compare connection establishment, data transmission, and congestion control.
- Visualize and interpret the behaviour of each protocol under various network conditions.
- Document and evaluate the effectiveness and performance of each protocol.

---

## Theory

The **Transport Layer (Layer 4)** is responsible for end-to-end (process-to-process) communication. It provides error detection, flow control, and multiplexing through port numbers.

### UDP (User Datagram Protocol)
- **Connectionless** — no handshake before data transfer.
- No guaranteed delivery, ordering, or error correction.
- **Low overhead**, suitable for: DNS, VoIP, video streaming, gaming.
- Header: Source Port, Destination Port, Length, Checksum (8 bytes total).

### TCP (Transmission Control Protocol)
- **Connection-oriented** — uses a **3-way handshake** (SYN → SYN-ACK → ACK).
- Guarantees delivery, ordering, and error correction.
- Uses **sliding window** for flow control and **slow start + congestion avoidance** for congestion control.
- Suitable for: HTTP, FTP, email, SSH.

### SCTP (Stream Control Transmission Protocol)
- **Connection-oriented** — uses a **4-way handshake** (INIT → INIT-ACK → COOKIE-ECHO → COOKIE-ACK).
- Supports **multi-streaming** (multiple independent data streams within one connection).
- Supports **multi-homing** (multiple IP addresses per endpoint for redundancy).
- Suitable for: VoIP signaling (SS7 over IP), telecom applications.

| Feature | UDP | TCP | SCTP |
|---------|-----|-----|------|
| Connection | None | 3-way handshake | 4-way handshake |
| Reliability | No | Yes | Yes |
| Ordering | No | Yes | Per-stream |
| Multi-streaming | No | No | Yes |
| Multi-homing | No | No | Yes |
| Congestion Control | No | Yes | Yes |
| Overhead | Very Low | Medium | Higher |
| Use Case | Streaming, DNS | Web, FTP | Telecom signaling |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Full topology: 6 PCs, 2 Switches, 1 Router |
| `screenshots/udp_simulation.png` | UDP packets in simulation mode |
| `screenshots/tcp_handshake.png` | TCP 3-way handshake (SYN/SYN-ACK/ACK) visible in PDU |
| `screenshots/tcp_ftp_transfer.png` | FTP data transfer over TCP |
| `screenshots/sctp_annotation.png` | SCTP explanation and annotation on workspace |

**Topology:**
```
PC0 (UDP client) ─┐                        ┌─ PC3 (UDP server)
PC1 (TCP client) ─┤── Switch0 ── Router ── Switch1 ─┤── PC4 (TCP server)
PC2 (SCTP doc)  ─┘                        └─ PC5 (SCTP doc)

Network 1: 192.168.1.0/24  |  Network 2: 192.168.2.0/24
```

---

## Step-by-Step Procedure

### Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 6 PCs, 2 Switches, 1 Router.
3. Connect: PC0, PC1, PC2 → Switch0; PC3, PC4, PC5 → Switch1.
4. Connect Switch0 → Router Fa0/0; Switch1 → Router Fa0/1.
5. Configure Router interfaces (see Configuration Commands).
6. Assign IPs to all PCs.

### A. Simulate UDP Communication
1. On workspace, **right-click → Add Note**:
   > "UDP: Connectionless. No handshake. PC0 sends a datagram to PC3 directly. Fast but unreliable — no retransmission on loss."
2. On PC0 → Desktop → Command Prompt:
   ```
   ping 192.168.2.2
   ```
   Ping uses ICMP which runs on top of IP (like UDP — no connection setup).
3. Switch to **Simulation Mode** → filter for ICMP.
4. Observe: PC0 sends packet directly to PC3 without any connection setup.
5. Click on the ICMP packet → **PDU Information** → observe Layer 3 IP header (no TCP handshake).

### B. Simulate TCP Communication
1. Add note:
   > "TCP: Connection-oriented. PC1 first performs 3-way handshake with PC4 (SYN → SYN-ACK → ACK), then transfers data reliably."
2. On Server (PC4) — configure it as a server: go to **Desktop → Services** (or use a Server device instead of PC).
3. If using Server device:
   - Enable **HTTP** service on PC4/Server.
4. On PC1 → Desktop → **Web Browser** → enter `http://192.168.2.3`.
5. Switch to **Simulation Mode** → filter for **TCP** and **HTTP**.
6. Click **Capture/Forward** → observe:
   - **SYN** from PC1 → PC4
   - **SYN-ACK** from PC4 → PC1
   - **ACK** from PC1 → PC4 (handshake complete)
   - **HTTP GET** request
   - **HTTP Response** with web page data

### C. SCTP (Documentation + Annotation)
1. Add workspace annotation:
   > "SCTP (RFC 4960): Uses 4-way handshake (INIT→INIT-ACK→COOKIE-ECHO→COOKIE-ACK). Supports multi-streaming — multiple independent ordered streams in one connection. Supports multi-homing — endpoint can have multiple IP addresses for failover. Used in telecom (e.g., SIGTRAN for SS7 over IP)."
2. Add a second note with comparison:
   > "SCTP vs TCP: SCTP avoids head-of-line blocking (HoLB) via multi-streaming. TCP has HoLB — one slow stream blocks all others."
3. *(Cisco Packet Tracer does not natively simulate SCTP — SCTP is documented conceptually with annotations and diagrams.)*

---

## Configuration Commands

```bash
# === ROUTER CONFIGURATION ===
enable
configure terminal
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
interface fastEthernet 0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit
end
show ip interface brief

# === PC IP ASSIGNMENTS ===
# PC0: 192.168.1.2 / 255.255.255.0 | GW: 192.168.1.1  (UDP client)
# PC1: 192.168.1.3 / 255.255.255.0 | GW: 192.168.1.1  (TCP client)
# PC2: 192.168.1.4 / 255.255.255.0 | GW: 192.168.1.1  (SCTP doc)
# PC3: 192.168.2.2 / 255.255.255.0 | GW: 192.168.2.1  (UDP server)
# PC4: 192.168.2.3 / 255.255.255.0 | GW: 192.168.2.1  (TCP/HTTP server)
# PC5: 192.168.2.4 / 255.255.255.0 | GW: 192.168.2.1  (SCTP doc)

# === UDP TEST (ICMP as UDP analog) ===
ping 192.168.2.2       # from PC0 — connectionless, like UDP

# === TCP TEST ===
# PC1 Web Browser → http://192.168.2.3
# Observe SYN → SYN-ACK → ACK in Simulation Mode

# === TCP 3-WAY HANDSHAKE PORT INFO ===
# Source Port: Ephemeral (e.g., 1025)
# Destination Port: 80 (HTTP)
# Flags: SYN=1 ACK=0 (first packet)
#        SYN=1 ACK=1 (server reply)
#        SYN=0 ACK=1 (client ack)

# === SCTP 4-WAY HANDSHAKE (documented) ===
# Client → Server: INIT (chunk)
# Server → Client: INIT-ACK (with State Cookie)
# Client → Server: COOKIE-ECHO
# Server → Client: COOKIE-ACK (association established)
```

---

## Observations / Results

| Protocol | Connection Setup | Data Transfer | ACK Required | Simulation Result |
|----------|----------------|--------------|-------------|------------------|
| UDP | None | Direct datagram | No | ✅ ICMP echo without handshake |
| TCP | 3-way handshake | Reliable, ordered | Yes | ✅ SYN/SYN-ACK/ACK visible |
| SCTP | 4-way handshake | Multi-stream | Yes | Documented (not in PT natively) |

**TCP PDU Details Observed:**
- HTTP GET request visible in Layer 7 of PDU Information.
- TCP sequence numbers increment with each data segment.
- ACK numbers confirm cumulative receipt.

> See `screenshots/tcp_handshake.png` for 3-way handshake PDU breakdown.

---

## Conclusion

Transport layer protocols UDP, TCP, and SCTP were successfully simulated and compared using Cisco Packet Tracer. UDP demonstrated low-overhead, connectionless delivery suitable for real-time applications. TCP's 3-way handshake and reliable delivery were clearly visualized via HTTP session simulation in Packet Tracer's Simulation Mode. SCTP, while not natively supported in Packet Tracer, was documented thoroughly with its unique advantages of multi-streaming and multi-homing — making it the preferred choice in telecommunications. The experiment reinforced how protocol selection at the transport layer fundamentally determines application reliability and performance.
