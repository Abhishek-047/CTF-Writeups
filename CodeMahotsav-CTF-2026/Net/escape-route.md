## **CH4-NET-02 - Escape Route**

### **700**

Created by Sujhal Gurav

2 (100% liked)  0

Data's staged. Now Ghost Lotus needs a way out that won't trip the obvious alarms. Network traffic from the affected host looks completely normal at first glance no red flags in the usual places.

Something's tunneling out through a protocol nobody inspects closely.

Evidence file: exfil.pcap

`SHA1: 041c1d0b40f0316af2e10d020e44229f3e0c3036`

# CH4-NET-02 - Escape Route

- **CTF:** Cyber Madness CTF 2026

- **Category:** Network Forensics

- **Points:** 700

- **Difficulty:** Hard

- **Status:** Solved ✅

## 📝 Description

> Data's staged. Now Ghost Lotus needs a way out that won't trip the obvious alarms. Network traffic from the affected host looks completely normal at first glance no red flags in the usual places.

---

## 🔍 Initial Analysis

The provided evidence was a packet capture (`.pcap`) file containing network traffic from a compromised host. At first glance, the traffic appeared to consist of legitimate browsing activity with DNS queries, HTTP requests, and other common protocols.

The challenge description hinted that the attacker was using a protocol that often escapes close inspection, suggesting a covert communication channel rather than obvious data transfers.

The primary objective was to determine how Ghost Lotus exfiltrated data and identify the hidden clue.

---

## 🛠️ Tools Used

- Wireshark

- Tshark

- Strings

- DNS Protocol Analysis

---

## 🚀 Approach

The first step was to inspect the packet capture in Wireshark and review the protocol hierarchy. There were no suspicious HTTP uploads, FTP sessions, or encrypted tunnels that immediately stood out.

Since the description specifically mentioned a protocol that "nobody inspects closely," the investigation shifted toward DNS traffic. DNS is commonly abused for covert channels because security teams often allow and overlook large volumes of DNS queries.

Filtering DNS packets revealed an unusual sequence of repetitive DNS requests with structured subdomains, indicating a DNS tunneling technique rather than normal hostname resolution.

Although many requests appeared legitimate, the DNS activity clearly represented data being encapsulated inside DNS queries for covert exfiltration.

From this observation, the intended answer was that Ghost Lotus escaped using a **DNS tunnel**.

---

## 🏁 Solving Steps

1. Opened `exfil.pcap` in Wireshark.

1. Reviewed the Protocol Hierarchy and Conversations statistics.

1. Verified that HTTP and other common protocols contained no obvious exfiltration.

1. Filtered DNS traffic using:

```plain text
dns
```

1. Observed repetitive and suspicious DNS queries containing structured subdomains, consistent with DNS tunneling.

1. Concluded that the attacker was exfiltrating data through DNS.

1. Submitted the corresponding flag.

---

## 🚩 Flag

```plain text
CM26{dns_tunnel_detected}
```

---

## 💡 What I Learned

- DNS is a common covert channel used for data exfiltration.

- Packet captures that appear completely normal may still hide malicious activity inside DNS queries.

- Wireshark's protocol hierarchy and display filters are extremely useful for narrowing down suspicious traffic.

- Challenge descriptions often provide subtle hints that point toward the protocol or attack technique being used.

---

## 🔗 References

- Wireshark DNS Analysis Documentation

- MITRE ATT&CK – DNS Tunneling (T1071.004)

- SANS DFIR Packet Analysis Resources
