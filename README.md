# Scanning-Networks

## 1. Overview of Network Scanning

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
