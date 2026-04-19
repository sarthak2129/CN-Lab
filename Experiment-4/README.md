# Experiment 4 — Error Detection and Correction Using Block Coding and CRC

---

## Experiment Title / Aim
Implement **error detection and correction** mechanisms using **Block Coding** and **Cyclic Redundancy Check (CRC)**. Simulate a communication system in Cisco Packet Tracer to demonstrate how errors are detected and corrected during data transmission.

---

## Objective
- Apply fundamental concepts of error detection and correction in data communication.
- Implement Block Coding (parity-based) and CRC error detection mechanisms.
- Simulate a communication system to demonstrate detection and correction of bit errors.
- Analyse the effectiveness of these mechanisms in real-time data transmission.

---

## Theory

**Error Detection** identifies corrupted bits in received data. **Error Correction** goes further by locating and fixing the corrupted bits.

### Block Coding (Parity Bits)
A block of data bits is appended with **parity bits** before transmission. The receiver recalculates parity and compares with the received parity bits. Single-bit errors can be detected (even parity) or corrected (Hamming Code).

- **Even Parity:** The total number of 1-bits (including parity) must be even.
- **Hamming Code:** Adds multiple parity bits at positions that are powers of 2, enabling single-bit error correction.

### Cyclic Redundancy Check (CRC)
CRC treats the data as a polynomial and divides it by a **generator polynomial**. The remainder (CRC value) is appended to the data. The receiver performs the same division — a non-zero remainder indicates an error.

| Mechanism | Errors Detected | Errors Corrected | Overhead |
|-----------|----------------|-----------------|---------|
| Simple Parity | Single-bit | None | 1 bit/block |
| Hamming Code | Single-bit | Single-bit | log₂(n) bits |
| CRC-8 | Burst errors up to 8 bits | None | 8 bits |
| CRC-32 | Burst errors up to 32 bits | None | 32 bits |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Network with 3 PCs and 1 Switch |
| `screenshots/simulation_packets.png` | ICMP packets in simulation mode |
| `screenshots/parity_note.png` | Workspace annotation explaining block coding |
| `screenshots/crc_annotation.png` | Workspace annotation explaining CRC process |

**Topology Used:**
```
PC0 ── Switch ── PC1
              └── PC2

Network: 192.168.1.0/24
```

---

## Step-by-Step Procedure

### Setting Up the Network
1. Open Cisco Packet Tracer → **File → New**.
2. Add devices: 3 PCs, 1 Switch (Cisco 2960).
3. Connect all PCs to the Switch using **Copper Straight-Through** cables.
4. Assign IP addresses to each PC.

### Implementing Block Coding for Error Detection
1. On the workspace, **right-click → Add Note**.
2. Add the following explanation:
   > "Block Coding in use: Data is divided into fixed-size blocks. Each block has parity bits appended. For example, data block `1011001` gets even parity bit `1` → transmitted as `10110011`. Receiver recalculates parity to detect errors."
3. Use the **Add Simple PDU** tool to simulate ICMP (ping) traffic from PC0 to PC1.
4. In **Simulation Mode**, observe the packet flow. Add another note indicating:
   > "Each ICMP packet's data field is protected by IP checksum — analogous to block coding parity."

### Implementing CRC
1. Add a new note to the workspace explaining CRC:
   > "CRC Process: Sender appends CRC remainder to data. Receiver divides the received frame by the same generator polynomial. Zero remainder = no error. Non-zero remainder = error detected."
2. Example manual CRC calculation (annotate on workspace):
   - Data: `11010011101100`
   - Generator: `1011`
   - Append 3 zeros → `11010011101100000`
   - XOR division → Remainder (CRC) appended to data
   - Transmitted frame = Data + CRC
3. Use **Add Simple PDU** from PC0 to PC2 in Simulation Mode.
4. Click on the ICMP packet in the Event List → **PDU Information** → observe the checksum field — this represents CRC in IP layer.

### Simulate and Observe Error Detection
1. Switch to **Simulation Mode**.
2. Filter events to show only **ICMP** packets.
3. Click **Capture/Forward** to step through packet transmission.
4. Observe successful delivery — confirming no errors detected by checksum.

---

## Configuration Commands

```bash
# === PC IP CONFIGURATION ===
# PC0: IP 192.168.1.1 | Mask 255.255.255.0
# PC1: IP 192.168.1.2 | Mask 255.255.255.0
# PC2: IP 192.168.1.3 | Mask 255.255.255.0

# === VERIFY CONNECTIVITY (PC Desktop → Command Prompt) ===
ping 192.168.1.2      # PC0 to PC1
ping 192.168.1.3      # PC0 to PC2

# === MANUAL CRC EXAMPLE (for documentation) ===
# Data bits:      1 1 0 1 0 0 1 1
# Generator:      1 0 1 1   (x^3 + x + 1)
# Padded data:    1 1 0 1 0 0 1 1 0 0 0
# XOR steps produce remainder → CRC
# Transmitted:    Data + CRC remainder

# === HAMMING CODE EXAMPLE (7,4) ===
# Data: D3 D5 D6 D7 = 1 0 1 1
# Parity bits at positions 1, 2, 4:
#   P1 = D3 XOR D5 XOR D7 = 1 XOR 0 XOR 1 = 0
#   P2 = D3 XOR D6 XOR D7 = 1 XOR 1 XOR 1 = 1
#   P4 = D5 XOR D6 XOR D7 = 0 XOR 1 XOR 1 = 0
# Transmitted codeword: P1 P2 D3 P4 D5 D6 D7 = 0 1 1 0 0 1 1
```

---

## Observations / Results

| Test | Expected | Actual | Error Detected? |
|------|----------|--------|----------------|
| PC0 → PC1 ICMP ping | Reply received | ✅ Reply received | No error (checksum valid) |
| PC0 → PC2 ICMP ping | Reply received | ✅ Reply received | No error |
| Manual CRC on `11010011101100` | Remainder = `100` | `100` ✅ | CRC calculated correctly |
| Hamming (7,4) on `1011` | Codeword `0110011` | `0110011` ✅ | Parity verified |

> See `screenshots/simulation_packets.png` for PDU Information checksum fields.

---

## Conclusion

Error detection and correction mechanisms were successfully implemented and demonstrated. Block coding (using parity bits and Hamming Code) can detect and correct single-bit errors with minimal overhead. CRC provides robust detection of burst errors and is widely used in Ethernet frames, USB, and storage devices. The Cisco Packet Tracer simulation confirmed that IP-layer checksums operate on the same principle as CRC, validating packet integrity during transmission.
