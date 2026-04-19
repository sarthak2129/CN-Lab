# Experiment 7 — Sliding Window Protocol with Piggybacking

---

## Experiment Title / Aim
Implement a **sliding window protocol with piggybacking** for efficient data transmission and error control. Simulate data transfer between two nodes and visualize the window movements and acknowledgments using Cisco Packet Tracer.

---

## Objective
- Apply fundamental networking concepts to understand sliding window protocols.
- Design and implement the sliding window protocol with piggybacking.
- Simulate and visualize data transfer, window movements, and acknowledgments.
- Analyse the efficiency and error control mechanisms of the protocol.
- Evaluate the impact of different window sizes on data transmission performance.

---

## Theory

### Sliding Window Protocol
A sliding window protocol allows the **sender to transmit multiple frames** before receiving acknowledgments, as long as the number of unacknowledged frames does not exceed the **window size (W)**. The window "slides" forward as ACKs are received.

- **Sender Window:** Range of sequence numbers the sender may transmit without waiting for ACK.
- **Receiver Window:** Sequence numbers the receiver is ready to accept.
- As each ACK is received, the window slides forward by one position.

### Piggybacking
**Piggybacking** is the technique of attaching an acknowledgment to an outgoing data frame. Instead of sending a separate ACK packet, the receiver includes the ACK in the next data frame it sends back. This reduces overhead and improves channel utilization.

**Without piggybacking:** Data frame (A→B) + Separate ACK frame (B→A)  
**With piggybacking:** Data frame (B→A) includes ACK for the frame received from (A→B)

| Parameter | Without Piggybacking | With Piggybacking |
|-----------|---------------------|--------------------|
| Frames transmitted | 2N (data + ACK) | ~N (combined) |
| Bandwidth use | High | Lower |
| Complexity | Simple | Moderate |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Two-PC network with switch |
| `screenshots/window_movement.png` | Simulation showing multiple PDUs in flight |
| `screenshots/piggybacked_ack.png` | PDU info showing combined data+ACK |
| `screenshots/event_list.png` | Event list with sequence of frames |

**Topology:**
```
PC0 (Sender/Receiver) ── Switch ── PC1 (Receiver/Sender)

Network: 192.168.1.0/24
Window Size W = 4 (example)
```

---

## Step-by-Step Procedure

### Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 2 PCs, 1 Switch.
3. Connect PC0 → Switch → PC1 (Straight-Through cables).
4. Assign IPs:
   - PC0: `192.168.1.1 / 255.255.255.0`
   - PC1: `192.168.1.2 / 255.255.255.0`

### Step 1: Add Sliding Window Explanation
1. **Right-click → Add Note** on workspace:
   > "Sliding Window Protocol: Sender transmits up to W=4 frames before needing ACK for Frame 0. Window moves forward as ACKs are received. Sequence numbers: 0, 1, 2, 3 → wrap around."

### Step 2: Add Piggybacking Explanation
2. Add another note:
   > "Piggybacking: PC1's response data frames include ACKs for the frames received from PC0. This reduces the number of separate ACK packets and improves channel efficiency."

### Step 3: Simulate Data Transfer
3. On PC0, open **Desktop → Command Prompt**.
4. Use the **Add Simple PDU** tool to create 4 consecutive packets from PC0 to PC1 (simulating a window of 4).
5. Switch to **Simulation Mode** → Filter for ICMP.
6. Click **Capture/Forward** repeatedly to step through the simulation.

### Step 4: Observe Window Movements
7. In Simulation Mode, observe that multiple frames (PDUs) are in transit simultaneously.
8. As each frame reaches PC1, note that PC1 would send an ACK back.
9. Observe the ICMP Reply from PC1 — this represents the piggybacked ACK embedded in the return data frame.

### Step 5: Analyse the Transfer
10. Click on individual packets in the **Event List**.
11. In **PDU Information**, view Layer 4 (TCP if applicable) or Layer 3 to observe sequence numbers and acknowledgment numbers — these represent the sliding window positions.

---

## Configuration Commands

```bash
# === PC IP CONFIGURATION ===
# PC0: 192.168.1.1 / 255.255.255.0
# PC1: 192.168.1.2 / 255.255.255.0

# === CONNECTIVITY TEST ===
ping 192.168.1.2

# === SLIDING WINDOW PARAMETERS (documented) ===
# Window Size (W)     = 4 frames
# Sequence Numbers    = 0, 1, 2, 3 (mod W)
# Frame Size          = 100 bytes
# ACK Type            = Cumulative (ACK n = all frames up to n-1 received)

# === PIGGYBACKING ILLUSTRATION ===
# PC0 → PC1: [Data: Frame 0] [Data: Frame 1] [Data: Frame 2] [Data: Frame 3]
# PC1 → PC0: [Data: Frame 0 | ACK: 3]  ← ACK piggybacked in data frame
# PC0 → PC1: [Data: Frame 4] [Data: Frame 5] ... (window slides)

# === EFFICIENCY CALCULATION ===
# W = window size = 4
# a = propagation delay / transmission time = 5 (example)
# Without piggybacking: Efficiency = W/(1+2a) = 4/11 ≈ 36.4%
# With piggybacking:    Fewer control frames → higher effective throughput
```

---

## Observations / Results

| Observation | Detail |
|-------------|--------|
| Frames in flight at once | 4 (window size W=4) |
| ACK behavior | ICMP Reply = piggybacked ACK in simulation |
| Window sliding | After each ACK, new frame enters the window |
| Efficiency gain | Fewer standalone ACK packets observed |
| Retransmission | Occurs when simulated error (PDU deletion) is introduced |

> See `screenshots/window_movement.png` for multi-PDU in-flight visualization.

---

## Conclusion

The sliding window protocol with piggybacking was successfully implemented and simulated in Cisco Packet Tracer. The protocol significantly improves channel utilization compared to Stop-and-Wait by keeping multiple frames in transit simultaneously. Piggybacking further reduces overhead by combining data and acknowledgment into a single frame, which is the basis of modern bidirectional protocols such as TCP. The simulation visually confirmed window advancement and the efficiency benefits of combined data-ACK frames.
