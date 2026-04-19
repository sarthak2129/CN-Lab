# Experiment 10 — DNS and DDNS Simulation: Domain Name Resolution and Dynamic Updates

---

## Experiment Title / Aim
Implement a **DNS (Domain Name System)** and **DDNS (Dynamic Domain Name System)** simulation to demonstrate domain name resolution and dynamic updates. Create a client-server application within Cisco Packet Tracer that queries DNS for domain names and updates DNS records dynamically.

---

## Objective
- Understand and apply DNS and DDNS concepts for domain name resolution and updates.
- Demonstrate the configuration and operation of DNS and DDNS services in a simulated network.
- Create a client-server application that performs DNS queries and updates DNS records dynamically.
- Analyse the behaviour of DNS and DDNS in handling domain name resolution and updates.

---

## Theory

### DNS (Domain Name System)
DNS is a hierarchical naming system that translates human-readable **domain names** (e.g., `example.com`) into **IP addresses** (e.g., `192.168.1.2`). It functions as the "phone book" of the internet.

**DNS Resolution Process:**
1. Client queries its configured DNS server for a domain name.
2. DNS server checks its records.
3. If found (Authoritative), it replies with the IP address.
4. If not found, it forwards the query to a higher-level DNS server (Recursive Resolution).

**DNS Record Types:**
| Record | Purpose | Example |
|--------|---------|---------|
| A | IPv4 address mapping | `example.com → 192.168.1.2` |
| AAAA | IPv6 address mapping | `example.com → 2001:db8::2` |
| CNAME | Alias | `www → example.com` |
| MX | Mail server | `mail.example.com` |
| PTR | Reverse DNS | `192.168.1.2 → example.com` |

### DDNS (Dynamic DNS)
DDNS allows DNS records to be **updated automatically** when a device's IP address changes. This is useful when a device uses a dynamic IP (via DHCP) but still needs a consistent domain name.

**DDNS Use Cases:** Home routers with dynamic ISP IPs, IoT devices, remote server access.

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Full topology: DNS Server, 3 PCs, Switch, Router |
| `screenshots/dns_config.png` | DNS records configured on Server |
| `screenshots/dns_query_browser.png` | PC0 Web Browser resolving example.com |
| `screenshots/nslookup_output.png` | PC1 nslookup result for test.com |
| `screenshots/ddns_update.png` | Updated DNS record and re-query result |

**Topology:**
```
PC0 ─┐
PC1 ─┤── Switch ── Router ── Server (DNS + DDNS + HTTP + DHCP)
PC2 ─┘

Network: 192.168.1.0/24
Server IP: 192.168.1.1
```

---

## Step-by-Step Procedure

### Setup
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 3 PCs, 1 Switch, 1 Router, 1 Server.
3. Connect all devices via Switch. Server connects to Switch directly.
4. Assign static IP to Server: `192.168.1.1 / 255.255.255.0`.
5. Assign IPs to PCs:
   - PC0: `192.168.1.2 / 255.255.255.0`, Gateway: `192.168.1.1`
   - PC1: `192.168.1.3 / 255.255.255.0`, Gateway: `192.168.1.1`
   - PC2: DHCP (auto-assigned from Server)

### Step 1: Configure DNS on the Server
1. Click on **Server → Services tab → DNS**.
2. Toggle **DNS Service: ON**.
3. Add DNS records:
   | Name | Type | Address |
   |------|------|---------|
   | `example.com` | A Record | `192.168.1.2` |
   | `test.com` | A Record | `192.168.1.3` |
   | `server.local` | A Record | `192.168.1.1` |
4. Click **Add** after each entry.

### Step 2: Configure DNS Server on PCs
1. PC0 → Desktop → IP Configuration:
   - DNS Server: `192.168.1.1`
2. PC1 → Desktop → IP Configuration:
   - DNS Server: `192.168.1.1`

### Step 3: Query DNS from PC0 (Browser)
1. PC0 → Desktop → **Web Browser**.
2. In address bar, type: `http://example.com` → press Enter.
3. Observe: Browser contacts DNS server → receives IP `192.168.1.2` → attempts HTTP connection.
4. In **Simulation Mode** (filter DNS + HTTP), observe:
   - DNS Query from PC0 → Server.
   - DNS Response: `example.com = 192.168.1.2`.
   - HTTP GET to `192.168.1.2`.

### Step 4: Query DNS from PC1 (nslookup)
1. PC1 → Desktop → **Command Prompt**.
2. Run:
   ```
   nslookup test.com
   ```
3. Observe output: Server address `192.168.1.1` and response IP `192.168.1.3`.

### Step 5: Dynamic DNS Update (DDNS Simulation)
1. On Server → Services → DNS:
   - **Add new record:** `newsite.com → 192.168.1.4`
   - **Update record:** Change `example.com` from `192.168.1.2` to `192.168.1.5`
2. On PC0 → Command Prompt:
   ```
   nslookup example.com
   ```
   Observe the updated IP `192.168.1.5` is returned.
3. On PC1 → Web Browser → type `http://newsite.com`
   Observe DNS resolves `newsite.com` to `192.168.1.4`.
4. Add workspace note:
   > "DDNS: DNS records are updated dynamically when IP addresses change (e.g., DHCP reassignment). In production, a DDNS client on the host sends update requests to the DNS server automatically. This simulates that behavior."

### Step 6: Enable HTTP on Server (for browser tests)
1. Server → Services → HTTP → **ON**.
2. Modify the `index.html` content:
   ```html
   <html><body><h1>Welcome to CN Lab DNS Server</h1></body></html>
   ```
3. Retry browser navigation from PC0 — the page should load.

---

## Configuration Commands

```bash
# === SERVER DNS RECORDS (configured via GUI) ===
# DNS Service: ON
# Record 1: example.com  → 192.168.1.2  (Type: A)
# Record 2: test.com     → 192.168.1.3  (Type: A)
# Record 3: server.local → 192.168.1.1  (Type: A)
# Record 4: newsite.com  → 192.168.1.4  (Type: A) [added for DDNS demo]

# === PC IP CONFIGURATION ===
# PC0: IP 192.168.1.2 | Mask 255.255.255.0 | GW 192.168.1.1 | DNS 192.168.1.1
# PC1: IP 192.168.1.3 | Mask 255.255.255.0 | GW 192.168.1.1 | DNS 192.168.1.1

# === ROUTER INTERFACE ===
enable
configure terminal
interface fastEthernet 0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
end

# === DNS QUERY TESTS ===
# PC0 Command Prompt:
nslookup example.com      # Should return 192.168.1.2
nslookup test.com         # Should return 192.168.1.3
nslookup newsite.com      # Should return 192.168.1.4 (after DDNS update)

# PC0 Web Browser:
# http://example.com      # DNS resolves → HTTP GET to 192.168.1.2
# http://newsite.com      # DNS resolves → HTTP GET to 192.168.1.4

# === DDNS UPDATE (via Server GUI) ===
# Change example.com from 192.168.1.2 → 192.168.1.5
# Verify: nslookup example.com now returns 192.168.1.5
```

---

## Observations / Results

| Query | DNS Response | Result | Notes |
|-------|-------------|--------|-------|
| `nslookup example.com` | `192.168.1.2` | ✅ Correct | A record resolved |
| `nslookup test.com` | `192.168.1.3` | ✅ Correct | A record resolved |
| Browser: `http://example.com` | IP `192.168.1.2` | ✅ Page loaded | DNS + HTTP working |
| After DDNS update: `nslookup example.com` | `192.168.1.5` | ✅ Updated | Dynamic update confirmed |
| `nslookup newsite.com` | `192.168.1.4` | ✅ New record resolved | DDNS new entry verified |

> See `screenshots/nslookup_output.png` and `screenshots/ddns_update.png` for evidence.

---

## Conclusion

DNS and DDNS were successfully configured and simulated in Cisco Packet Tracer. DNS correctly resolved domain names to IP addresses, as verified through both the Web Browser and nslookup command. Dynamic DNS updates were demonstrated by modifying server records and confirming that subsequent queries returned the updated values. This experiment illustrated how DNS underpins all internet navigation, and how DDNS enables consistent hostname access for hosts with dynamic IP addresses — a critical feature for modern cloud services and home networking.
