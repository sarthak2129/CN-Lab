# Experiment 6 — Simulation of Multiple Access Protocols

---

## Experiment Title / Aim
Develop a simulation to demonstrate multiple access protocols — **Pure ALOHA**, **Slotted ALOHA**, **CSMA/CD**, and **CSMA/CA** — using Cisco Packet Tracer. Analyse the performance of each protocol in handling network collisions and maximizing data transmission efficiency.

---

## Objective
- Understand and apply fundamental networking concepts related to multiple access protocols.
- Design and implement simulations for Pure ALOHA, Slotted ALOHA, CSMA/CD, and CSMA/CA.
- Analyse performance in terms of collision handling and data transmission efficiency.
- Compare the effectiveness of each protocol under varying network conditions.

---

## Theory

Multiple access protocols control how multiple devices share a single communication channel.

### Pure ALOHA
Devices transmit whenever they have data. If a collision occurs, they wait a random time and retransmit. **Maximum throughput ≈ 18.4%** of channel capacity.

### Slotted ALOHA
Time is divided into slots. Devices can only transmit at the beginning of a slot. **Maximum throughput ≈ 36.8%** — double that of Pure ALOHA.

### CSMA/CD (Carrier Sense Multiple Access / Collision Detection)
Used in **wired Ethernet (IEEE 802.3)**. Devices listen before transmitting. If a collision is detected mid-transmission, all devices stop and wait a random time (binary exponential backoff) before retrying.

### CSMA/CA (Carrier Sense Multiple Access / Collision Avoidance)
Used in **wireless networks (IEEE 802.11 / Wi-Fi)**. Devices listen first, then wait an additional random time (backoff) before transmitting to *avoid* collisions proactively. Uses ACK frames to confirm delivery.

| Protocol | Collision Handling | Medium | Max Efficiency |
|----------|--------------------|--------|---------------|
| Pure ALOHA | Detect via timeout | Any | ~18.4% |
| Slotted ALOHA | Detect via timeout | Any | ~36.8% |
| CSMA/CD | Detect during TX | Wired | ~50-80% |
| CSMA/CA | Avoid before TX | Wireless | ~70%+ |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/csma_cd_topology.png` | Wired hub-based network for CSMA/CD |
| `screenshots/csma_ca_topology.png` | Wireless access point for CSMA/CA |
| `screenshots/collision_simulation.png` | Simulation mode showing collision at hub |
| `screenshots/aloha_annotation.png` | Workspace annotation for ALOHA protocols |

**CSMA/CD Topology:**
```
PC0 ─┐
PC1 ─┤── Hub ── (Shared Medium — collision domain)
PC2 ─┘

Network: 192.168.1.0/24
```

**CSMA/CA Topology:**
```
PC3 ─ ─ ─ ─ ─┐
PC4 ─ ─ ─ ─ ─┤── Wireless Access Point (AP)
PC5 ─ ─ ─ ─ ─┘

SSID: CN-Lab-WiFi | Network: 192.168.2.0/24
```

---

## Step-by-Step Procedure

### A. Pure ALOHA & Slotted ALOHA (Annotations)
1. On the workspace, **right-click → Add Note**, create annotations:
   > "Pure ALOHA: Any device transmits whenever ready. Collisions resolved by random backoff. Efficiency ≈ 18.4%."
   > "Slotted ALOHA: Transmission restricted to time slot boundaries. Efficiency ≈ 36.8%. Used in satellite communication."
2. Use **Add Simple PDU** from multiple PCs simultaneously to demonstrate collisions.

### B. CSMA/CD Simulation
1. Add: 3 PCs, 1 **Hub** (not a switch — hubs share collision domain).
2. Connect all PCs to the Hub using **Copper Straight-Through** cables.
3. Assign IPs:
   - PC0: `192.168.1.1`, PC1: `192.168.1.2`, PC2: `192.168.1.3` (Mask: `255.255.255.0`)
4. Switch to **Simulation Mode**.
5. Filter events: show only **ARP** and **ICMP**.
6. Send **Add Simple PDU** from PC0 → PC2 AND PC1 → PC2 simultaneously.
7. Observe collision detection at the Hub — Cisco Packet Tracer will show the collision indicator.
8. Observe retransmission after backoff.

### C. CSMA/CA Simulation (Wireless)
1. Add: 3 PCs (or laptops), 1 **Wireless Access Point** (Linksys WRT300N or similar).
2. Change PC NICs to wireless (Laptop → Config → Remove Ethernet NIC → Add Wireless NIC).
3. Configure AP:
   - SSID: `CN-Lab-WiFi`
   - Security: None (for simplicity)
4. Configure PCs to connect to the AP:
   - PC Desktop → PC Wireless → Connect to `CN-Lab-WiFi`
5. Assign IPs: `192.168.2.1`, `192.168.2.2`, `192.168.2.3`.
6. In Simulation Mode, observe that wireless devices wait and use RTS/CTS mechanism before transmitting (CSMA/CA).
7. Send simultaneous PDUs and observe — no collisions due to avoidance mechanism.

---

## Configuration Commands

```bash
# === CSMA/CD NETWORK — PC IPs ===
# PC0: 192.168.1.1 / 255.255.255.0
# PC1: 192.168.1.2 / 255.255.255.0
# PC2: 192.168.1.3 / 255.255.255.0
# Hub: No configuration needed (layer 1 device)

# === CSMA/CA WIRELESS NETWORK ===
# AP SSID: CN-Lab-WiFi | Channel: 6 | Security: Open
# PC3: 192.168.2.1 / 255.255.255.0
# PC4: 192.168.2.2 / 255.255.255.0
# PC5: 192.168.2.3 / 255.255.255.0

# === CONNECTIVITY TESTS ===
ping 192.168.1.3     # Wired — CSMA/CD
ping 192.168.2.3     # Wireless — CSMA/CA

# === COLLISION DOMAIN NOTE ===
# Hub = single collision domain (CSMA/CD applies)
# Switch = separate collision domain per port (no CSMA/CD needed)
# Wireless AP = CSMA/CA used instead of CD
```

---

## Observations / Results

| Protocol | Medium | Collision Observed | Backoff | Efficiency |
|----------|--------|--------------------|---------|-----------|
| Pure ALOHA | Shared (simulated) | Yes | Random delay | ~18.4% |
| Slotted ALOHA | Shared (simulated) | Less frequent | Slot-aligned | ~36.8% |
| CSMA/CD | Wired Hub | Yes (detected mid-TX) | Binary exponential | Higher |
| CSMA/CA | Wireless AP | No (avoided via backoff) | Random + RTS/CTS | High |

> See `screenshots/collision_simulation.png` for Hub collision visualization.

---

## Conclusion

All four multiple access protocols were successfully simulated and compared. Pure ALOHA and Slotted ALOHA demonstrate the tradeoff between simplicity and efficiency on shared channels. CSMA/CD (wired Ethernet) improves efficiency significantly by detecting collisions and retransmitting quickly. CSMA/CA (Wi-Fi) avoids collisions altogether, making it indispensable for wireless networks where collision detection is not feasible due to the half-duplex nature of radio transmission.
