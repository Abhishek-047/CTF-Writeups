## **CH3-NET-01 - Network Mapping**

### **400**

Created by Sujhal Gurav

1 (100% liked)  0

With harvested credentials in hand, Ghost Lotus started mapping the internal network probing what's reachable, what's segmented, and what's worth reaching. A packet capture from this window shows the reconnaissance in progress.

Somewhere in this traffic, one internal host stands out from the rest the one Ghost Lotus was actually building a path toward.

Evidence file: capture.pcap (plus network_map.csv for cross-reference)

# CH3-NET-01 - Network Mapping

- **CTF:** Cyber Madness CTF 2026

- **Category:** Network Forensics

- **Points:** 400

- **Difficulty:** Medium

- **Status:** Solved ✅

---

## 📝 Description

> With harvested credentials in hand, Ghost Lotus started mapping the internal network probing what's reachable, what's segmented, and what's worth reaching. A packet capture from this window shows the reconnaissance in progress.

---

## 🔍 Initial Analysis

The challenge provided two evidence files:

- **capture.pcap** containing network traffic generated during the reconnaissance phase.

- **network_map.csv** containing an inventory of internal hosts, their VLANs, and network roles.

The objective was to determine which internal host Ghost Lotus was targeting while performing network mapping.

---

## 🛠️ Tools Used

- Wireshark

- tshark

- tcpdump

- Linux command line

- Spreadsheet viewer (for `network_map.csv`)

---

## 🚀 Approach

I first examined the network topology described in `network_map.csv` to understand the internal infrastructure.

Next, I analyzed the packet capture and filtered the traffic to identify reconnaissance activity such as SMB connections, host discovery, and communication between different VLANs.

One destination repeatedly stood out during the investigation.

Cross-referencing the observed traffic with the network map showed that **LCORE-01** was a highly restricted internal server that became the focus of Ghost Lotus' reconnaissance.

Since the challenge specifically asked for the internal host Ghost Lotus was building a path toward, **LCORE-01** was identified as the correct answer.

---

## 🏁 Solving Steps

1. Opened `capture.pcap` in Wireshark.

1. Reviewed `network_map.csv` to understand hostnames, IP addresses, VLANs, and server roles.

1. Filtered network traffic to locate reconnaissance attempts and unusual internal communications.

1. Identified traffic directed toward the protected internal server **LCORE-01**.

1. Confirmed that this host matched the challenge description as the primary reconnaissance target.

1. Submitted the hostname as the flag.

---

## 🚩 Flag

```plain text
CM26{LCORE-01}
```

---

## 💡 What I Learned

- Packet captures can reveal an attacker's reconnaissance objectives even without exploitation traffic.

- Correlating packet captures with network documentation is often essential during forensic investigations.

- High-value assets are frequently identified by observing where reconnaissance traffic is concentrated rather than by simply counting packets.

---

## 🔗 References

- [https://www.wireshark.org/docs/](https://www.wireshark.org/docs/)

- [https://wiki.wireshark.org/DisplayFilters](https://wiki.wireshark.org/DisplayFilters)

- [https://www.tcpdump.org/](https://www.tcpdump.org/)

---

# Cyber Madness CTF 2026

- **Organizer:** Cyber Madness

- **Rank:** #10

- **Dates:** July 2026

---

## 🏆 Summary

Cyber Madness CTF 2026 featured challenges across multiple cybersecurity domains, including Web Exploitation, Digital Forensics, Cryptography, Reverse Engineering, Network Analysis, and OSINT. This challenge focused on forensic analysis of network traffic, requiring participants to correlate packet captures with network documentation to identify the attacker's intended target.

---

## 🚩 Challenges

---

## 📓 Quick Notes

- Always correlate packet captures with network documentation.

- Focus on destination hosts during reconnaissance analysis.

- Restricted servers often become the primary targets during lateral movement.

- Wireshark display filters greatly simplify investigation of large packet captures.

---

## 🧠 Key Learnings

- Network reconnaissance leaves identifiable traces in packet captures.

- Cross-referencing multiple evidence sources significantly improves forensic accuracy.

- Understanding network segmentation and server roles is critical for identifying attacker objectives.

- Reconnaissance analysis is an important step in reconstructing the attack timeline and predicting lateral movement.
