# Experiment 3 — Network Simulator for Analyzing Packet Delay, Loss, and Throughput

---

## Experiment Title / Aim
Develop a network simulator using Cisco Packet Tracer to analyse **packet delay**, **packet loss**, and **end-to-end throughput**. Implement various routing algorithms and measure their impact on network performance under different traffic conditions.

---

## Objective
- Analyse packet delay, loss, and throughput using Cisco Packet Tracer.
- Implement and compare the performance of Static Routing, RIP, and OSPF routing protocols.
- Evaluate the impact of different traffic conditions on network performance.
- Optimize network configurations to improve performance metrics.

---

## Theory

**Packet Delay** is the time taken for a packet to travel from source to destination. It includes transmission delay, propagation delay, processing delay, and queuing delay.

**Packet Loss** occurs when packets fail to reach the destination due to congestion, buffer overflow, or link failure.

**Throughput** is the actual rate of successful data delivery over a communication channel, measured in bits per second (bps).

### Routing Algorithms

| Algorithm | Type | Key Feature |
|-----------|------|-------------|
| **Static Routing** | Manual | Admin-defined paths; no overhead |
| **RIP** | Dynamic, Distance-Vector | Uses hop count; max 15 hops |
| **OSPF** | Dynamic, Link-State | Uses Dijkstra's algorithm; fastest convergence |

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Full network topology — 2 switches, 1 router, 4 PCs |
| `screenshots/router_interfaces.png` | Router interface status after configuration |
| `screenshots/ping_pc0_to_pc1.png` | Successful ICMP ping across subnets |
| `screenshots/simulation_event_list.png` | Simulation mode event list showing delay timestamps |

**Topology Used:**
```
PC0 ─┐                           ┌─ PC2
     Switch0 ── Router ── Switch1
PC1 ─┘                           └─ PC3

Network 1: 192.168.1.0/24  |  Network 2: 192.168.2.0/24
```

---

## Step-by-Step Procedure

1. Open Cisco Packet Tracer → **File → New**.
2. Add devices:
   - 4 PCs, 2 Switches (Cisco 2960), 1 Router (Cisco 1941)
3. Connect:
   - PC0 → Switch0, PC1 → Switch0 (Straight-Through)
   - PC2 → Switch1, PC3 → Switch1 (Straight-Through)
   - Switch0 → Router Fa0/0, Switch1 → Router Fa0/1 (Straight-Through)
4. Configure IP addresses on all PCs (see Configuration Commands).
5. Configure Router interfaces and enable ports.
6. Verify using `show ip interface brief` on the Router CLI.
7. Configure PC gateways.
8. **Test Connectivity:** Use Add Simple PDU (PC0 → PC2).
9. Switch to **Simulation Mode** → click **Capture/Forward** to step through packets.
10. Open the **Event List** to observe per-packet timestamps — note the delay.
11. Run multiple PDUs simultaneously to observe queuing and potential loss.
12. Implement **Static Routing** first, then compare with **RIP** (optional).

---

## Configuration Commands

```bash
# === ROUTER CONFIGURATION ===
enable
configure terminal

! LAN 1 Interface
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

! LAN 2 Interface
interface fastEthernet 0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown

exit
end

! Verify — you should see both interfaces UP/UP
show ip interface brief
```

```bash
# === STATIC ROUTING (if multiple routers) ===
enable
configure terminal
ip route 192.168.2.0 255.255.255.0 192.168.1.1
end
```

```bash
# === RIP ROUTING (optional) ===
enable
configure terminal
router rip
 version 2
 network 192.168.1.0
 network 192.168.2.0
 no auto-summary
end
```

```bash
# === PC COMMAND PROMPT ===
# PC0 configuration
# IP: 192.168.1.2 | Mask: 255.255.255.0 | Gateway: 192.168.1.1

# PC2 configuration
# IP: 192.168.2.2 | Mask: 255.255.255.0 | Gateway: 192.168.2.1

# Test
ping 192.168.2.2
ping 192.168.2.3
```

---

## Observations / Results

| Metric | Static Routing | RIP | Notes |
|--------|---------------|-----|-------|
| Packet Delay (1 hop) | ~1 ms | ~1 ms | Minimal |
| Packet Loss | 0% | 0% | Under normal load |
| Convergence Time | Instant | ~30 sec | RIP sends updates every 30s |
| Throughput | High | High | RIP adds slight overhead |

**Simulation Mode Observations:**
- Each ICMP packet's journey is visible step-by-step in the Event List.
- Timestamps confirm propagation and processing delays.
- Under high load (multiple simultaneous PDUs), slight queuing delay was observed at the router interface.

> See `screenshots/simulation_event_list.png` for timestamped event details.

---

## Conclusion

The network simulator successfully demonstrated and measured packet delay, loss, and throughput in Cisco Packet Tracer. Static routing provided the best performance with zero overhead. RIP, while adding periodic update traffic, correctly converged and maintained connectivity. The simulation confirmed that routing algorithm choice directly impacts convergence time, and traffic load affects queuing delay. OSPF would provide the fastest convergence in larger, more complex topologies.
