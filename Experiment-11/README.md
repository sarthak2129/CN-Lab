# Experiment 11 — Web Server Simulation: HTTP and WWW Basics

---

## Experiment Title / Aim
Create a simulation to demonstrate the workings of **HTTP (Hypertext Transfer Protocol)** and the **World Wide Web (WWW)**. Implement basic HTTP request and response handling and simulate a simple web browsing session in Cisco Packet Tracer to illustrate how web servers and clients interact to transfer web content.

---

## Objective
- Understand and apply HTTP concepts for web server and client interactions.
- Implement basic HTTP request and response handling in a simulated environment.
- Simulate a simple web browsing session to demonstrate web content retrieval.
- Analyse how HTTP handles web requests and responses between a web server and a client.
- Document and explain the workings of HTTP in a simulated network scenario.

---

## Theory

### HTTP (Hypertext Transfer Protocol)
HTTP is an **application-layer protocol** (Layer 7) used for transmitting hypermedia documents (web pages) over the internet. It follows a **request-response** model: the client sends an HTTP request, and the server responds with the requested resource.

**HTTP Methods:**
| Method | Purpose |
|--------|---------|
| GET | Retrieve a resource (web page, image, etc.) |
| POST | Submit data to a server (forms, login) |
| PUT | Update a resource |
| DELETE | Remove a resource |
| HEAD | GET without response body |

**HTTP Status Codes:**
| Code | Meaning |
|------|---------|
| 200 OK | Request successful |
| 301 Moved Permanently | Resource has moved |
| 404 Not Found | Resource does not exist |
| 500 Internal Server Error | Server-side error |

### HTTP Request-Response Cycle
1. Client opens TCP connection to server (Port 80 for HTTP, Port 443 for HTTPS).
2. Client sends HTTP **GET** request: `GET /index.html HTTP/1.1`
3. Server processes request and sends back HTTP **200 OK** response with the web page.
4. Client renders the web page.
5. TCP connection is closed (or kept alive for persistent connections in HTTP/1.1).

### WWW (World Wide Web)
The WWW is a system of interlinked hypertext documents accessible via the Internet, using HTTP as its communication protocol. Web pages are written in **HTML (Hypertext Markup Language)** and identified by **URLs (Uniform Resource Locators)**.

---

## Network Topology

> Screenshots are stored in `screenshots/` folder.

| File | Contents |
|------|----------|
| `screenshots/topology.png` | Web server + 2 PCs + Switch topology |
| `screenshots/http_service_config.png` | Server HTTP service enabled with custom HTML |
| `screenshots/browser_pc0.png` | PC0 Web Browser displaying the hosted web page |
| `screenshots/browser_pc1.png` | PC1 Web Browser displaying the hosted web page |
| `screenshots/http_get_request.png` | HTTP GET packet visible in Simulation Mode |
| `screenshots/http_200_response.png` | HTTP 200 OK response packet details |
| `screenshots/tcp_handshake_http.png` | TCP 3-way handshake before HTTP session |

**Topology:**
```
PC0 (Web Client) ─┐
                  ├── Switch ── Server (Web Server: HTTP)
PC1 (Web Client) ─┘

Network: 192.168.1.0/24
Server IP: 192.168.1.1
```

---

## Step-by-Step Procedure

### Step 1: Setup the Network
1. Open Cisco Packet Tracer → **File → New**.
2. Add: 2 PCs, 1 Switch (Cisco 2960), 1 Server.
3. Connect:
   - PC0 → Switch (Copper Straight-Through, FastEthernet)
   - PC1 → Switch (Copper Straight-Through, FastEthernet)
   - Server → Switch (Copper Straight-Through, FastEthernet)
4. Wait for all link lights to turn green.

### Step 2: Assign IP Addresses
1. Click **Server → Desktop → IP Configuration**:
   - IP Address: `192.168.1.1`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: (leave blank — server is on same subnet)
2. Click **PC0 → Desktop → IP Configuration**:
   - IP Address: `192.168.1.2`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `192.168.1.1`
3. Click **PC1 → Desktop → IP Configuration**:
   - IP Address: `192.168.1.3`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `192.168.1.1`

### Step 3: Configure the Web Server
1. Click on **Server → Services tab → HTTP**.
2. Toggle **HTTP Service: ON**.
3. Toggle **HTTPS Service: ON** (optional).
4. Click on `index.html` in the file editor and modify content:
   ```html
   <html>
   <head><title>CN Lab Web Server</title></head>
   <body>
     <h1>Welcome to Computer Networks Lab</h1>
     <p>Experiment 11: HTTP and WWW Simulation</p>
     <p>Server IP: 192.168.1.1</p>
     <p>Protocol: HTTP/1.1</p>
   </body>
   </html>
   ```
5. Click **Save**.

### Step 4: Verify Network Connectivity
1. PC0 → Desktop → Command Prompt:
   ```
   ping 192.168.1.1
   ```
   Expected: 4 replies from `192.168.1.1` with TTL and time values.
2. PC1 → Desktop → Command Prompt:
   ```
   ping 192.168.1.1
   ```
   Both pings should succeed before proceeding.

### Step 5: Simulate HTTP Browsing from PC0
1. PC0 → Desktop → **Web Browser**.
2. In the address bar, type: `http://192.168.1.1`
3. Press **Enter** (or click Go).
4. Observe the web page loading: "Welcome to Computer Networks Lab".

### Step 6: Simulate HTTP Browsing from PC1
1. Repeat Step 5 on PC1.
2. Type `http://192.168.1.1` in PC1's Web Browser.
3. The same page should load — confirming the server handles multiple clients.

### Step 7: Monitor HTTP Traffic in Simulation Mode
1. Switch to **Simulation Mode** (bottom right clock icon).
2. Click **Edit Filters** → enable only **TCP** and **HTTP**.
3. On PC0, open Web Browser and navigate to `http://192.168.1.1`.
4. Click **Capture/Forward** (Play button) to step through packet by packet.
5. Observe the sequence:
   - **ARP:** PC0 broadcasts to resolve Server's MAC address.
   - **TCP SYN:** PC0 → Server (port 80) — connection initiation.
   - **TCP SYN-ACK:** Server → PC0 — connection accepted.
   - **TCP ACK:** PC0 → Server — handshake complete.
   - **HTTP GET:** PC0 → Server: `GET / HTTP/1.1`
   - **HTTP 200 OK:** Server → PC0 with HTML content.
   - **TCP FIN / ACK:** Connection teardown.
6. Click on the **HTTP GET** packet in the Event List → **PDU Information** → observe Layer 7 details.

---

## Configuration Commands

```bash
# === SERVER HTTP SERVICE (configured via GUI) ===
# Server → Services → HTTP → ON
# Server → Services → HTTPS → ON (optional)
# Edit index.html with custom HTML content

# === IP ASSIGNMENTS ===
# Server: 192.168.1.1 / 255.255.255.0
# PC0:    192.168.1.2 / 255.255.255.0 | GW 192.168.1.1
# PC1:    192.168.1.3 / 255.255.255.0 | GW 192.168.1.1

# === CONNECTIVITY TESTS (PC Command Prompt) ===
ping 192.168.1.1         # Verify server reachability from PC0 and PC1

# === HTTP REQUEST FLOW (observed in Simulation Mode) ===
# Step 1: ARP Request  — PC0 broadcasts: "Who has 192.168.1.1?"
# Step 2: ARP Reply    — Server: "192.168.1.1 is at [MAC]"
# Step 3: TCP SYN      — PC0 → Server:80  [SYN, seq=0]
# Step 4: TCP SYN-ACK  — Server → PC0     [SYN, ACK, seq=0, ack=1]
# Step 5: TCP ACK      — PC0 → Server     [ACK, seq=1, ack=1]
# Step 6: HTTP GET     — PC0 → Server     [GET / HTTP/1.1]
#                        Host: 192.168.1.1
#                        Connection: keep-alive
# Step 7: HTTP 200 OK  — Server → PC0     [HTTP/1.1 200 OK]
#                        Content-Type: text/html
#                        Body: <html>...</html>
# Step 8: TCP FIN/ACK  — Connection teardown

# === EXPECTED WEB PAGE CONTENT ===
# Title: CN Lab Web Server
# Body:  Welcome to Computer Networks Lab
#        Experiment 11: HTTP and WWW Simulation
```

---

## Observations / Results

| Test | Expected | Result | Notes |
|------|----------|--------|-------|
| `ping 192.168.1.1` from PC0 | 4 replies | ✅ Success | Layer 3 connectivity confirmed |
| `ping 192.168.1.1` from PC1 | 4 replies | ✅ Success | Both clients can reach server |
| PC0 Browser → `http://192.168.1.1` | Web page loads | ✅ Success | HTML content displayed |
| PC1 Browser → `http://192.168.1.1` | Web page loads | ✅ Success | Multiple clients served |
| HTTP GET packet in Simulation Mode | Visible at Layer 7 | ✅ Observed | `GET / HTTP/1.1` captured |
| HTTP 200 OK response | Visible in Event List | ✅ Observed | HTML body in PDU info |
| TCP 3-way handshake | SYN → SYN-ACK → ACK | ✅ Observed | Before HTTP request |

**PDU Information Details (from Simulation Mode):**
- Src Port: `1025` (ephemeral, PC0)
- Dst Port: `80` (HTTP, Server)
- Protocol: `TCP → HTTP`
- HTTP Version: `HTTP/1.1`
- Status Code: `200 OK`

> See `screenshots/http_get_request.png` and `screenshots/http_200_response.png`.

---

## Conclusion

A complete HTTP web server simulation was successfully implemented in Cisco Packet Tracer. The web server correctly served HTML content to multiple clients simultaneously. Simulation Mode provided a clear, step-by-step visualization of the entire HTTP transaction: ARP resolution, TCP 3-way handshake, HTTP GET request, HTTP 200 OK response, and TCP connection teardown. This experiment demonstrated how the WWW functions at the protocol level, and how HTTP (built on TCP) provides reliable, ordered delivery of web content. The hands-on simulation confirms the layered operation of the OSI model — from physical layer (cable links) all the way to the application layer (HTTP).
