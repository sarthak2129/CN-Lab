# Experiment 2 — Simulation and Comparison of Packet Switching and Circuit Switching

---

## Experiment Title / Aim
Create a network simulation to demonstrate **Packet Switching** and **Circuit Switching**, and compare the performance and efficiency of both methods by simulating data transmission scenarios in Cisco Packet Tracer.

---

## Objective
- Apply fundamental networking concepts to understand packet switching and circuit switching.
- Design and implement network simulations for both switching methods using Cisco Packet Tracer.
- Analyse and compare the performance and efficiency of both switching methods.
- Understand the practical applications and limitations of both approaches.

---

## Theory

**Circuit Switching** establishes a dedicated communication path between sender and receiver before data transfer begins (e.g., traditional telephone networks). The path is reserved for the entire duration of the session.

**Packet Switching** breaks data into small packets. Each packet is routed independently through the network and may take different paths to reach the destination (e.g., the Internet). Packets are reassembled at the destination.

| Feature | Circuit Switching | Packet Switching |
|---------|-------------------|-----------------|
| Path | Dedicated, fixed | Dynamic, per-packet |
| Bandwidth | Reserved | Shared |
| Delay | Low and constant | Variable (queuing) |
| Efficiency | Low (idle capacity wasted) | High |
| Example | PSTN phone calls | Internet / IP networks |
| Setup time | Required before transfer | None |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/packet_switching_topology.png` | Packet switching network in Cisco Packet Tracer |
| `screenshots/circuit_switching_topology.png` | Circuit switching simulation layout |
| `screenshots/packet_pdu_flow.png` | PDU flow in simulation mode — packet switching |
| `screenshots/circuit_persistent_ping.png` | Persistent ping simulating dedicated circuit |

---

## Step-by-Step Procedure

### A. Packet Switching Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add devices:
   - 4 PCs (End Devices panel)
   - 2 Switches (Cisco 2960)
   - 1 Router (Cisco 1941)
3. Connect:
   - PC0, PC1 → Switch0 (Copper Straight-Through)
   - PC2, PC3 → Switch1 (Copper Straight-Through)
   - Switch0 → Router Fa0/0 (Straight-Through)
   - Switch1 → Router Fa0/1 (Straight-Through)
4. Assign IP addresses:
   - PC0: `192.168.1.2 / 255.255.255.0`, Gateway: `192.168.1.1`
   - PC1: `192.168.1.3 / 255.255.255.0`, Gateway: `192.168.1.1`
   - PC2: `192.168.2.2 / 255.255.255.0`, Gateway: `192.168.2.1`
   - PC3: `192.168.2.3 / 255.255.255.0`, Gateway: `192.168.2.1`
5. Configure router interfaces (see Configuration Commands).
6. Use **Add Simple PDU** to send packets from PC0 to PC2; switch to **Simulation Mode** to observe independent packet routing.

### B. Circuit Switching Simulation
1. Add 2 additional PCs and 1 more Switch to the same workspace or a new one.
2. Connect the new PCs to the new Switch; connect the Switch to the existing Router.
3. Assign IPs:
   - PC4: `192.168.3.2 / 255.255.255.0`, Gateway: `192.168.3.1`
   - PC5: `192.168.3.3 / 255.255.255.0`, Gateway: `192.168.3.1`
4. On PC4 Desktop → Command Prompt, issue a persistent ping to PC5:
   ```
   ping 192.168.3.3 -t
   ```
   This continuous ping simulates a dedicated reserved path (circuit) between the two devices.
5. Switch to Simulation Mode → observe how the path remains fixed for every ICMP echo.

---

## Configuration Commands

```bash
# === ROUTER CONFIGURATION (Packet Switching) ===
enable
configure terminal

! Interface towards Switch0 (Network 192.168.1.0)
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

! Interface towards Switch1 (Network 192.168.2.0)
interface fastEthernet 0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown

! Interface towards Switch2 (Circuit Switching Network)
interface fastEthernet 1/0
 ip address 192.168.3.1 255.255.255.0
 no shutdown

exit
end

! Verify
show ip interface brief
show ip route
```

```bash
# === PC COMMAND PROMPT — Test Connectivity ===
ping 192.168.2.2           # Packet switching: PC0 → PC2
ping 192.168.3.3 -t        # Circuit switching simulation: persistent ping
```

---

## Observations / Results

| Test | Method | Result | Observation |
|------|--------|--------|-------------|
| PC0 → PC2 single PDU | Packet Switching | ✅ Success | Packets routed independently |
| PC4 → PC5 persistent ping | Circuit Switching (sim) | ✅ Success | Same path maintained for all ICMP |
| Multiple simultaneous PDUs | Packet Switching | ✅ Success | Shared bandwidth, varied paths |
| Interrupting circuit ping | Circuit Switching (sim) | Path lost | No data flows when circuit is "down" |

> See `screenshots/packet_pdu_flow.png` and `screenshots/circuit_persistent_ping.png`.

---

## Conclusion

Packet switching and circuit switching were successfully simulated in Cisco Packet Tracer. Packet switching demonstrated higher efficiency by dynamically routing individual packets through the best available path, making it ideal for modern data networks. Circuit switching, simulated via persistent pings, showed the concept of a dedicated reserved path suitable for latency-sensitive applications like voice calls. For general data communication, **packet switching is more efficient** due to its ability to share bandwidth across multiple simultaneous flows.
