# Experiment 8 — Simulation of Logical Addressing Using IPv4 and IPv6

---

## Experiment Title / Aim
Create a simulation to demonstrate **logical addressing using IPv4 and IPv6**. Implement address mapping techniques — **ARP**, **RARP**, **BOOTP**, and **DHCP** — to show how devices acquire and resolve network addresses using Cisco Packet Tracer.

---

## Objective
- Understand and apply logical addressing concepts for IPv4 and IPv6.
- Implement and demonstrate ARP, RARP, BOOTP, and DHCP address mapping techniques.
- Analyse how devices acquire and resolve network addresses using different techniques.
- Compare and contrast the functionality and application of different address mapping protocols.

---

## Theory

**IPv4** uses 32-bit addresses (e.g., `192.168.1.1`). **IPv6** uses 128-bit addresses (e.g., `2001:db8::1`) to accommodate the growing number of internet-connected devices.

### Address Mapping Protocols

| Protocol | Full Name | Direction | Purpose |
|----------|-----------|-----------|---------|
| **ARP** | Address Resolution Protocol | IP → MAC | Resolves IP address to MAC address |
| **RARP** | Reverse ARP | MAC → IP | Resolves MAC address to IP address (legacy) |
| **BOOTP** | Bootstrap Protocol | MAC → IP+config | Provides IP + gateway to diskless workstations |
| **DHCP** | Dynamic Host Config Protocol | Auto | Dynamically assigns IP, mask, gateway, DNS |

**ARP Process:** PC broadcasts "Who has 192.168.1.2? Tell 192.168.1.1". The target responds with its MAC address. ARP table is then updated.

**DHCP Process (DORA):** Discover → Offer → Request → Acknowledge.

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Full network with Server, Router, Switch, PCs |
| `screenshots/arp_request.png` | ARP broadcast in simulation mode |
| `screenshots/dhcp_dora.png` | DHCP Discover → Offer → Request → ACK flow |
| `screenshots/ipv6_ping.png` | Successful IPv6 ping between devices |
| `screenshots/arp_table.png` | ARP table on PC after resolution |

**Topology:**
```
PC0 ─┐                          ┌─ Server (DHCP + BOOTP + RARP + DNS)
PC1 ─┤── Switch ── Router ── ──┤
PC2 ─┘                          └─ (Router also handles inter-VLAN)

IPv4 Subnet 1: 192.168.1.0/24
IPv6 Subnet 1: 2001:db8:1::/64
Server IP:     192.168.1.1
```

---

## Step-by-Step Procedure

### Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 3 PCs, 1 Switch, 1 Router, 1 Server.
3. Connect PCs and Server to Switch (Straight-Through); Switch to Router.
4. Assign static IP to Server: `192.168.1.1 / 255.255.255.0`.

### A. ARP Simulation
1. Assign static IPs to PC0 (`192.168.1.2`) and PC1 (`192.168.1.3`).
2. On PC0 → Desktop → Command Prompt:
   ```
   arp -a          (view current ARP table — likely empty)
   ping 192.168.1.3
   arp -a          (ARP table now shows PC1's MAC address)
   ```
3. Switch to **Simulation Mode** → filter for **ARP** events.
4. Click **Capture/Forward** → observe ARP broadcast from PC0 and unicast reply from PC1.

### B. RARP (demonstration via annotation)
1. Add workspace note:
   > "RARP: A diskless workstation knows only its own MAC address. It broadcasts a RARP request. The RARP server maps the MAC to an IP and replies. RARP is now obsolete — replaced by BOOTP/DHCP."
2. (Cisco Packet Tracer does not natively support RARP service — document conceptually with annotations.)

### C. BOOTP Simulation
1. On Server → **Services → DHCP** → enable BOOTP compatibility.
2. Add a BOOTP entry mapping a specific MAC to a fixed IP.
3. Configure PC2 to obtain IP automatically.
4. In Simulation Mode, observe the BOOTP Discover and Offer packets.

### D. DHCP Simulation
1. On Server → **Services → DHCP**:
   - Enable DHCP.
   - Pool Name: `LAN_POOL`
   - Default Gateway: `192.168.1.1`
   - DNS Server: `192.168.1.1`
   - Starting IP: `192.168.1.10`
   - Subnet Mask: `255.255.255.0`
   - Maximum Users: `50`
   - Click **Add**.
2. On PC0 → Desktop → IP Configuration → select **DHCP**.
3. Observe that PC0 receives an IP automatically.
4. In Simulation Mode, observe the **DORA** process:
   - **Discover:** PC0 broadcasts to find DHCP servers.
   - **Offer:** Server offers IP `192.168.1.10`.
   - **Request:** PC0 requests the offered IP.
   - **Acknowledge:** Server confirms the assignment.

### E. IPv6 Configuration
1. On PC0 → Desktop → IP Configuration → IPv6 section:
   - IPv6 Address: `2001:db8:1::2/64`
   - IPv6 Default Gateway: `2001:db8:1::1`
2. On Server:
   - IPv6 Address: `2001:db8:1::1/64`
3. Test IPv6 connectivity from PC0 Command Prompt:
   ```
   ping 2001:db8:1::1
   ```

---

## Configuration Commands

```bash
# === DHCP SERVER SETUP (Server GUI) ===
# Services → DHCP → Enable
# Pool: LAN_POOL
# Gateway: 192.168.1.1
# DNS: 192.168.1.1
# Start IP: 192.168.1.10
# Mask: 255.255.255.0
# Max Users: 50

# === ROUTER CONFIGURATION (for inter-subnet routing) ===
enable
configure terminal
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 ipv6 address 2001:db8:1::1/64
 no shutdown
exit
ipv6 unicast-routing
end

# === PC COMMAND PROMPT TESTS ===
arp -a                         # View ARP cache
ping 192.168.1.3               # IPv4 connectivity test
ping 2001:db8:1::1             # IPv6 connectivity test
nslookup example.com           # DNS test (if configured)

# === ARP TABLE VERIFICATION ===
# After ping, run:
arp -a
# Expected output:
#   192.168.1.3    00:D0:58:XX:XX:XX   dynamic
```

---

## Observations / Results

| Protocol | Test | Result | Notes |
|----------|------|--------|-------|
| ARP | PC0 pings PC1 → `arp -a` | ✅ MAC resolved | ARP cache updated |
| DHCP | PC2 set to DHCP | ✅ IP assigned | Received `192.168.1.10` |
| BOOTP | Server BOOTP entry | ✅ Fixed IP assigned | Based on MAC binding |
| IPv6 | PC0 pings Server IPv6 | ✅ Reply received | `2001:db8:1::1` reachable |
| RARP | Conceptual demo | Documented | Not supported natively in Packet Tracer |

> See `screenshots/dhcp_dora.png` and `screenshots/arp_request.png` for visual evidence.

---

## Conclusion

IPv4 and IPv6 addressing with all major address mapping protocols were successfully simulated in Cisco Packet Tracer. ARP's broadcast-based MAC resolution was clearly visualized in Simulation Mode. DHCP's DORA process demonstrated automatic IP configuration, which is the standard method in modern networks. BOOTP provides fixed IP mapping by MAC, useful for servers and network equipment. IPv6 addressing was configured and tested successfully alongside IPv4, demonstrating dual-stack capability.
