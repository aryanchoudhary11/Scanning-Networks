# Scanning-Networks

## Network Scanning Concepts 

### 1. Overview of Network Scanning

**👉 Definition:** Network scanning is the process of sending packets to a target network/system to identify:

- **Live hosts** (who is up and running?)
- **Open ports** (where can I knock?)
- **Services** (what’s listening on those ports?)
- **OS/versions** (what type of system am I dealing with?)

**⚡ Real-world analogy:** Imagine you’re a burglar (attacker) walking down a street of houses (network).

- Footprinting was you checking **which houses exist** in that street.
- Scanning is you walking up to each house and **knocking on every door/window** (ports) to see which ones respond.

**Types of Network Scanning**

**1. Ping Sweep** → To find live hosts. Example: ```ping -c 1 192.168.1.1```

**2. Port Scanning** → To check which ports are open. Example: ```nmap -p 1-1000 192.168.1.1```

**3. Service Scanning** → To identify what service/version runs on open ports. Example: **nmap -sV 192.168.1.1**

**4. OS Fingerprinting** → To guess the operating system. Example: ```nmap -O 192.168.1.1```

**⚠️ Why important?**

- Attackers use it for finding entry points.
- Defenders use it for hardening systems and detecting unauthorized scans.

### 2. TCP Communication Flags

**👉 Background:** TCP (Transmission Control Protocol) is connection-oriented. Before two computers talk, they must establish a connection using the **3-way handshake.**

**📌 TCP Flags**
Flags are tiny “switches” in TCP packets that control communication.

|   Flag   |      Name      |          Function          |       Example Use       |
|----------|----------------|----------------------------|-------------------------|
| **SYN**  | Synchronize    | Initiates a connection     | First step in handshake |
| **ACK**  | Acknowledgment | Confirms received data     | Step 2 & 3 in handshake |
| **FIN**  | Finish         | Politely ends a connection | “Goodbye” signal        |
| **RST**  | Reset          | Abruptly ends a connection | If port is closed       |
| **PSH**  | Push           | Sends data immediately     | Streaming/chat apps     |
| **URG**  | Urgent         | High-priority data         | 	Rare, used in VoIP     |

**⚡ TCP 3-way Handshake Example:**

1. Client → Server: **SYN** (Hey, I want to connect!)

2. Server → Client: **SYN+ACK** (Sure, let’s connect!)

3. Client → Server: **ACK** (Cool, we’re connected!)

✅ Connection established → Now data can flow.

**⚡ How Hackers Use Flags in Scanning**

Attackers manipulate flags to “trick” systems and detect open ports.

- **SYN Scan (nmap -sS)** → Half-open scan, stealthy, doesn’t complete handshake.
- **FIN Scan (nmap -sF)** → Sends FIN packet. Closed ports reply with RST, open ports stay silent.
- **NULL Scan (nmap -sN)** → Sends packet with no flags.
- **XMAS Scan (nmap -sX)** → Sends packet with FIN+PSH+URG.

**⚠️ Real-world use:**

- Security teams detect attackers if they see many SYN or XMAS packets in logs.
- Attackers use these scans because some firewalls respond differently → revealing information.

### 3. TCP/IP Communication

**👉 TCP/IP model** = the backbone of the internet.
It has 4 layers (simplified OSI model):

|       Layer         |  Example Protocols   |           Function           |
|---------------------|----------------------|------------------------------|
|   **Application**   | HTTP, FTP, SMTP, DNS | End-user interaction         | 
|    **Transport**    |       TCP, UDP       | Reliable/unreliable delivery | 
|    **Internet**     |       IP, ICMP       | Addressing & routing         |
| **Network Access**  |   Ethernet, Wi-Fi    | Physical transmission        | 

**TCP vs UDP**

- **TCP (Connection-oriented)** → Reliable (like WhatsApp double ticks). Uses handshake.

- **UDP (Connectionless)** → Fast but unreliable (like live streaming). No handshake.

**⚡ Example:**

- **TCP** → Banking apps (need reliability).
- **UDP** → Online games, VoIP (need speed).

**Real-world Example of TCP/IP in Scanning**

- Attacker sends a **SYN** to port 80 (HTTP).
- If server replies with **SYN+ACK**, port 80 is open → attacker can try exploits like SQLi on the web app.
- If server replies with **RST**, port is closed.
- If server ignores, firewall is blocking.

✅ That’s how tools like **Nmap, Masscan, ZMap** work under the hood.

### 🔑 Key Takeaways

- **Network scanning** = mapping active systems, ports, and services.
- **TCP flags** are manipulated by attackers for stealth scans.
- **TCP/IP model** explains how data moves across the internet.
- Understanding this is **critical** because every attack after scanning (exploitation, privilege escalation, etc.) depends on this knowledge.

---

## Scanning Tools

### 1. Nmap (Network Mapper)

**👉 Most popular network scanning tool.**

- Open-source, runs on Linux/Windows/Mac.
- Used for port scanning, service detection, OS fingerprinting, and scriptable scans (NSE).

**📌 Key Features:**

- **Host Discovery** → Find live systems.
- **Port Scanning** → Open, closed, filtered ports.
- **Service Version Detection** → e.g., Apache 2.4.29.
- **OS Fingerprinting** → Linux/Windows version guess.
- **NSE Scripts** → Automate tasks (brute force, vuln detection).

**⚡ Real-world Example:**

```
nmap -sS -sV -O 192.168.1.10
```

- ```-sS``` → SYN stealth scan
- ```-sV``` → Detect service version
- ```-O``` → OS fingerprinting

✅ Used daily by penetration testers and also by defenders to audit networks.

### 2. Hping3

👉 A **packet crafting tool** (command-line).

- Unlike Nmap (which automates scans), Hping3 gives **manual control** over TCP/IP packets.
- Good for **firewall testing, IDS evasion, and custom scans**.

**📌 Key Features:**

- Send TCP, UDP, ICMP packets with custom flags.
- Perform **traceroute** with different protocols.
- Test firewall rules and IDS detection.

**⚡ Example:** SYN flood (DoS test)

```
hping3 -S --flood -V -p 80 192.168.1.10
```

- Sends continuous SYN packets to port 80.
- Tests if server can handle SYN flood attacks.

### 3. Hping Scan with AI

👉 Modern use-case: Combine **AI automation** with Hping.

- AI can generate packet crafting scripts based on scanning goals.
- Example: Instead of manually setting flags, AI suggests the right combinations for stealth scans.

**⚡ Example:**

You ask AI → “Scan for open web ports stealthily”.

AI generates:
```
hping3 -S -p 80,443,8080 --scan 192.168.1.0/24
```

- Saves time and reduces human error.

✅ This is becoming popular in **red team automation.**

### 4. Metasploit Framework

👉 A **penetration testing platform** with built-in scanners.

- Mostly known for exploitation, but also has **auxiliary scanners.**
- Example: SMB scanner, SSH login brute force, port scanners.

**⚡ Example (inside Metasploit):**
```
msfconsole
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.168.1.0/24
set PORTS 1-1000
run
```

- Scans all hosts in subnet for open TCP ports.

✅ Advantage → After scanning, you can immediately exploit vulnerable services within the same framework.

### 5. NetScanTools Pro

👉 A **Windows-based commercial toolkit** for network discovery.

- GUI-based (easy for beginners).
- Combines multiple tools: ping sweeps, traceroute, port scanning, SNMP scanning, etc.
- Good for corporate environments where GUIs are preferred over CLI.

**📌 Real-world Use:**

- Security analysts in enterprises use it for **documentation and quick discovery.**
- Less stealthy than Nmap/Hping3, but very useful for IT auditing.


### 🔑 Key Takeaways

- **Nmap** → Standard port/service/OS scanner.
- **Hping3** → Custom packet crafting for advanced scans.
- **Hping + AI** → AI-assisted packet scans for automation.
- **Metasploit** → Integrated scanning + exploitation.
- **NetScanTools Pro** → GUI-based scanning for corporate/Windows users.

---

## Host Discovery

### 1. Host Discovery Techniques

👉 Methods used to check if a system is **alive (up)** or **dead (down).**

- Can be **ICMP-based** (ping)
- Can be **TCP/UDP-based** (knocking on ports)
- Can be **Layer 2-based** (ARP scans inside LAN)

📌 Real-world use:

- Attackers → Save time by scanning only live hosts.
- Defenders → Detect unauthorized scans in logs.

### 2. ARP Ping Scan

👉 Works in **Local Area Network (LAN)** only.

- Sends ARP requests (```Who has 192.168.1.10?```)
- Live host replies with its **MAC address.**

**⚡ Example:**
```
nmap -PR 192.168.1.0/24
```

✅ Always works in LAN (because ARP cannot be blocked by firewalls).

### 3. UDP Ping Scan

👉 Sends UDP packets to a host.

- If ICMP Port Unreachable is received → Host is alive.
- If no reply → Could be firewalled or dropped.

**⚡ Example:**
```
nmap -PU 192.168.1.0/24
```

### 4. ICMP ECHO Ping Scan

👉 The classic ping (ICMP Echo Request).

- If host replies with ICMP Echo Reply → It’s alive.

**⚡ Example:**
```
nmap -PE 192.168.1.0/24
```

⚠️ Many networks block ICMP to prevent ping sweeps.

### 5. ICMP ECHO Ping Sweep

👉 Same as above, but scans multiple hosts in a subnet.

- Used to find all live systems quickly.

**⚡ Example:**
```
fping -a -g 192.168.1.0/24
```

### 6. ICMP Timestamp Ping Scan

👉 Sends an ICMP Timestamp request.

- If host responds → It’s alive (and gives system clock info).

**⚡ Example:**
```
nmap -PP 192.168.1.0/24
```

### 7. ICMP Address Mask Ping Scan

👉 Sends an ICMP Address Mask request.

- Host replies with its subnet mask.
- Rarely enabled today.

**⚡ Example:**
```
nmap -PM 192.168.1.0/24
```

### 8. TCP SYN Ping Scan

👉 Sends a SYN packet to a specific port (e.g., 80).

- If reply = **SYN+ACK** → Host is alive.
- If reply = **RST** → Host is alive but port closed.

**⚡ Example:**
```
nmap -PS80 192.168.1.0/24
```

### 9. TCP ACK Ping Scan

👉 Sends a TCP ACK packet to a port.

- If reply = **RST** → Host is alive.
- Useful to bypass firewalls that block SYN packets.

**⚡ Example:**
```
nmap -PA80 192.168.1.0/24
```

### 10. IP Protocol Ping Scan

👉 Sends packets using different IP protocols (ICMP, TCP, UDP, GRE, etc.).

- If any reply → Host is alive.

**⚡ Example:**
```
nmap -PO 192.168.1.0/24
```

### 11. Host Discovery with AI

👉 AI can automate host discovery by:

- Selecting best scan method depending on environment.
- Running parallel scans and adapting if blocked.
- Example: AI tries ICMP first, then switches to ARP if no response.

**⚡ Real-world use:**

- Red Teams → Faster stealth scans.
- Blue Teams → AI alerts when unusual ping patterns appear.

### 12. Ping Sweep Tools

📌 Tools to automate host discovery across a range of IPs:

- **Nmap** → ```nmap -sn 192.168.1.0/24```
- **fping** → Faster ICMP sweeps.
- **Angry IP Scanner (GUI)** → Beginner-friendly.
- **NetScanTools Pro** → Enterprise-grade.

### 🔑 Key Takeaways

- **ARP scans** → Best for LAN.
- **ICMP scans** → Classic but often blocked.
- **TCP/UDP scans** → Work when ICMP is blocked.
- **IP protocol scans** → Catch hosts behind firewalls.
- **AI + scanning tools** → Automates stealth & efficiency.

---

## Port and Service Discovery

### 1. Port Scanning Techniques

👉 Port = communication endpoint (like a door to a house).

- **Open port** → service is listening (e.g., port 80 = web server).
- **Closed port** → no service listening.
- **Filtered port** → firewall blocking.

**⚡ Tools:** Nmap, Masscan, Unicornscan, ZMap.

### 2. TCP Connect / Full-Open Scan

👉 Completes full TCP 3-way handshake.

- Reliable but noisy (easy to detect in logs).

**⚡ Example:**
```
nmap -sT 192.168.1.10
```

### 3. Stealth Scan (Half-Open Scan)

👉 Sends **SYN** → gets **SYN+ACK** → replies with **RST** (instead of ACK).

- Never completes handshake → stealthier.

**⚡ Example:**
```
nmap -sS 192.168.1.10
```

### 4. Inverse TCP Flag Scan

👉 Sends unusual flags (FIN, URG, PSH) to detect open ports.

- Closed ports → reply with **RST**.
- Open ports → no reply.

**⚡ Example:**
```
nmap -sN 192.168.1.10   # NULL scan (no flags)
nmap -sF 192.168.1.10   # FIN scan
nmap -sX 192.168.1.10   # Xmas scan
```

### 5. Xmas Scan

👉 Sends FIN + PSH + URG (like a Christmas tree 🎄 lit up).

- Same logic as inverse scans.

⚠️ Works best on Unix/Linux systems, Windows often ignores.

### 6. TCP Maimon Scan

👉 Sends **FIN/ACK** → some BSD-based systems misbehave.

- Detects open ports if no reply.

**⚡ Example:**
```
nmap -sM 192.168.1.10
```

### 7. ACK Flag Probe Scan

👉 Sends **ACK** packet.

- Used to check if firewall rules exist.
- If **RST** = port unfiltered, if no reply = filtered.

**⚡ Example:**
```
nmap -sA 192.168.1.10
```

### 8. IDLE/IPID Header Scan

👉 Uses a third-party zombie host to scan target.

- Super stealthy because traffic looks like it’s coming from the zombie, not you.

**⚡ Example:**
```
nmap -sI zombie_host 192.168.1.10
```

### 9. UDP Scan

👉 Sends UDP packets.

- If ICMP Port Unreachable → port closed.
- If no reply → open or filtered.

**⚡ Example:**
```
nmap -sU 192.168.1.10
```

⚠️ Slower than TCP scans.

### 10. SCTP INIT Scan

👉 **SCTP** = Stream Control Transmission Protocol (used in telecom).

- INIT packet used to check open SCTP ports.

**⚡ Example:**
```
nmap -sY 192.168.1.10
```
