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
