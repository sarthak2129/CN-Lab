# Experiment 1 — Design and Simulation of Computer Network Topologies

---

## Experiment Title / Aim
Design and simulate a simple computer network using various connection topologies — **Bus, Star, Ring, and Mesh** — and compare their advantages and disadvantages in terms of data flow and network efficiency.

---

## Objective
- Apply fundamental networking concepts to develop and analyse different network topologies.
- Design and simulate network architectures for efficient data communication using Cisco Packet Tracer.
- Compare and contrast the efficiency and performance of various network topologies.
- Evaluate the impact of different topologies on real-time and multimedia applications.

---

## Theory

A **network topology** defines the physical or logical arrangement of devices in a computer network.

| Topology | Description | Advantage | Disadvantage |
|----------|-------------|-----------|--------------|
| **Bus** | All devices share a single communication line (backbone). | Simple, low cost | Single point of failure; performance drops with more devices |
| **Star** | All devices connect to a central hub/switch. | Easy to manage; fault isolated to one node | Hub/switch failure brings down the whole network |
| **Ring** | Each device connects to exactly two others forming a closed loop. | Orderly data transfer; no collisions | One broken link can disrupt the entire network |
| **Mesh** | Every device connects to every other device. | Highly redundant; fault tolerant | Expensive; complex wiring |

**Data Flow:** In a bus topology, data travels in both directions along the backbone; in star, through the central switch; in ring, uni-directionally around the loop; in mesh, via dedicated point-to-point links.

---

## Network Topology

> **Screenshots of all four topologies are stored in the `screenshots/` folder.**

| File | Contents |
|------|----------|
| `screenshots/bus_topology.png` | Bus topology layout in Cisco Packet Tracer |
| `screenshots/star_topology.png` | Star topology layout |
| `screenshots/ring_topology.png` | Ring topology layout |
| `screenshots/mesh_topology.png` | Mesh topology layout |
| `screenshots/ping_output.png` | Successful ping results between PCs |

---

## Step-by-Step Procedure

### Setting Up Cisco Packet Tracer
1. Download and install Cisco Packet Tracer from the official Cisco Networking Academy website.
2. Launch the application and click **File → New** to create a new workspace.

### A. Bus Topology
1. Drag and drop **4 PCs** and **1 Hub** onto the workspace from the End Devices panel.
2. Connect each PC to the Hub using **Copper Straight-Through** cables.
3. Click on each PC → **Desktop** tab → **IP Configuration** and assign IP addresses:
   - PC0: `192.168.1.1 / 255.255.255.0`
   - PC1: `192.168.1.2 / 255.255.255.0`
   - PC2: `192.168.1.3 / 255.255.255.0`
   - PC3: `192.168.1.4 / 255.255.255.0`
4. Use the **Add Simple PDU** tool (envelope icon) to send a ping from PC0 to PC3.
5. Switch to **Simulation Mode** and step through to observe packet flow.

### B. Star Topology
1. Drag **4 PCs** and **1 Switch** (e.g., Cisco 2960) onto the workspace.
2. Connect each PC to the Switch with **Copper Straight-Through** cables.
3. Assign IP addresses (same subnet: `192.168.2.x / 255.255.255.0`).
4. Test connectivity using `ping` in the **Command Prompt** on each PC's Desktop tab.

### C. Ring Topology
1. Drag **4 PCs** onto the workspace.
2. Connect them in a chain: PC0 → PC1 → PC2 → PC3 → PC0 using **Copper Cross-Over** cables.
3. Assign IP addresses (subnet: `192.168.3.x`).
4. Add a **note** on the workspace explaining the ring path.
5. Test connectivity and observe simulation mode for ring packet flow.

### D. Mesh Topology
1. Drag **4 PCs** onto the workspace.
2. Connect every PC to every other PC using **Copper Cross-Over** cables (6 cables total).
3. Assign IP addresses (subnet: `192.168.4.x`).
4. Send ping PDUs to test all paths.

---

## Configuration Commands

> Bus and Star topologies use plug-and-play switches/hubs — no CLI commands are needed. For Ring/Mesh simulated via routers, use the following pattern:

```bash
# On each Router interface (if applicable)
enable
configure terminal
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
end

# Verify
show ip interface brief
```

```bash
# On each PC — Command Prompt (Desktop tab)
# Test connectivity
ping 192.168.1.2
ping 192.168.1.3
ping 192.168.1.4
```

---

## Observations / Results

| Topology | Ping Success | Packet Path | Notes |
|----------|-------------|-------------|-------|
| Bus | ✅ Yes | All via hub (broadcast) | Collision domain shared |
| Star | ✅ Yes | Via central switch | Fastest switching; isolated links |
| Ring | ✅ Yes | Sequential around loop | Unidirectional frame travel |
| Mesh | ✅ Yes | Direct point-to-point | Most redundant path available |

> Refer to `screenshots/ping_output.png` for visual proof of successful ICMP replies.

---

## Conclusion

All four network topologies — Bus, Star, Ring, and Mesh — were successfully designed and simulated in Cisco Packet Tracer. The **Star topology** proved to be most practical for LAN environments due to its ease of management and fault isolation. The **Mesh topology** offered the highest reliability but at the cost of complexity. Bus and Ring topologies, while simpler to set up, suffer from single points of failure that make them less suitable for modern networks.
