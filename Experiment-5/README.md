# Experiment 5 — Simulation of Flow Control and Error Control Protocols

---

## Experiment Title / Aim
Design and simulate flow control and error control protocols — **Stop-and-Wait**, **Go-Back-N ARQ**, and **Selective Repeat ARQ** — using Cisco Packet Tracer. Compare their performance in terms of throughput and efficiency under varying network conditions.

---

## Objective
- Apply fundamental networking concepts to understand flow control and error control mechanisms.
- Design and implement Stop-and-Wait, Go-Back-N ARQ, and Selective Repeat ARQ protocols.
- Simulate and analyse protocol performance under different network conditions.
- Compare the throughput and efficiency of each protocol.

---

## Theory

### Stop-and-Wait ARQ
The sender transmits **one frame** and waits for an acknowledgment (ACK) before sending the next. If the ACK is not received within a timeout, the frame is retransmitted. Simple but inefficient for high-latency links.

**Efficiency = 1 / (1 + 2a)** where `a = propagation delay / transmission time`

### Go-Back-N ARQ
The sender can transmit up to **N frames** (window size N) without receiving an ACK. If an error occurs in frame `i`, all frames from `i` onward are retransmitted — even if some were received correctly.

**Efficiency = N / (1 + 2a)** if `N ≥ (1 + 2a)`, else `= 1`

### Selective Repeat ARQ
Only the **erroneous frame** is retransmitted. Correctly received out-of-order frames are buffered. More efficient than Go-Back-N but requires more receiver buffer space.

| Protocol | Window Size | Retransmission | Efficiency | Buffer |
|----------|------------|---------------|-----------|--------|
| Stop-and-Wait | 1 | Whole frame | Low | Minimal |
| Go-Back-N | N | N frames from error | Medium | Small |
| Selective Repeat | N | Erroneous frame only | High | Large |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Two-node network with switch |
| `screenshots/stop_wait_simulation.png` | Stop-and-Wait single PDU flow |
| `screenshots/go_back_n_simulation.png` | Go-Back-N multiple PDUs in flight |
| `screenshots/selective_repeat_simulation.png` | Selective Repeat PDU sequence |

**Topology:**
```
PC0 (Sender) ── Switch ── PC1 (Receiver)

Network: 192.168.1.0/24
```

---

## Step-by-Step Procedure

### Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 2 PCs, 1 Switch (Cisco 2960).
3. Connect PC0 and PC1 to the Switch via Straight-Through cables.
4. Assign IPs:
   - PC0: `192.168.1.1 / 255.255.255.0`
   - PC1: `192.168.1.2 / 255.255.255.0`

### A. Simulate Stop-and-Wait
1. On workspace, **right-click → Add Note**:
   > "Stop-and-Wait: Sender sends 1 frame, waits for ACK. Only one frame in transit at a time."
2. Use **Add Simple PDU** to send one packet from PC0 to PC1.
3. Switch to **Simulation Mode** → click **Capture/Forward** once.
4. Observe: PC0 sends the frame → PC1 receives → PC1 sends ACK → PC0 receives ACK.
5. Only after the ACK is received does the next PDU flow.

### B. Simulate Go-Back-N ARQ
1. Add workspace annotation:
   > "Go-Back-N ARQ: Window size N. Sender transmits N frames continuously. On error in frame i, frames i through N are retransmitted."
2. Send **multiple PDUs** rapidly from PC0 to PC1 using Add Simple PDU (send 4 PDUs in quick succession).
3. In Simulation Mode, observe the multiple frames in transit simultaneously.
4. Note that if you delete/cancel one PDU (simulating error), packets after that point are re-sent.
5. Document the sequence in the Event List.

### C. Simulate Selective Repeat ARQ
1. Add workspace annotation:
   > "Selective Repeat ARQ: Only the erroneous frame is retransmitted. Receiver buffers out-of-order correct frames."
2. Send PDUs and observe the simulation.
3. Compare with Go-Back-N: in Selective Repeat, only the failed frame is highlighted for retransmission.

---

## Configuration Commands

```bash
# === PC IP SETUP ===
# PC0: 192.168.1.1 / 255.255.255.0
# PC1: 192.168.1.2 / 255.255.255.0

# === VERIFY CONNECTIVITY ===
ping 192.168.1.2

# === PROTOCOL EFFICIENCY CALCULATIONS ===
# Stop-and-Wait: Window = 1
#   If propagation delay = 5ms, transmission time = 1ms
#   a = 5/1 = 5
#   Efficiency = 1/(1+2×5) = 1/11 ≈ 9%

# Go-Back-N: Window N = 7
#   Efficiency = 7/(1+2×5) = 7/11 ≈ 63.6%

# Selective Repeat: Window N = 7
#   Efficiency = Same formula, but no wasted retransmissions
#   Practical efficiency much higher due to no unnecessary retransmits
```

---

## Observations / Results

| Protocol | Frames in Transit | Retransmission Scope | Observed Behavior |
|----------|-----------------|---------------------|-------------------|
| Stop-and-Wait | 1 | Single frame | One PDU visible; ACK required before next |
| Go-Back-N | N (window) | Frame i + all subsequent | Multiple PDUs visible; all retransmit on error |
| Selective Repeat | N (window) | Only erroneous frame | Multiple PDUs visible; only failed one resent |

> Refer to `screenshots/` for visual evidence of each protocol's packet flow.

---

## Conclusion

All three flow and error control protocols were successfully simulated in Cisco Packet Tracer. Stop-and-Wait is the simplest but least efficient, suited only for very low-latency links. Go-Back-N improves utilization through pipelining but wastes bandwidth by retransmitting unnecessary frames. Selective Repeat ARQ offers the best efficiency at the cost of greater buffer complexity. For modern high-speed networks, **Selective Repeat** (or TCP's similar sliding-window mechanism) is the preferred approach.
