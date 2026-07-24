## **CH5-OSINT-03 - Following The Trail**

### **850**

Created by Sujhal Gurav

1 (100% liked)  0

LotusCore's real value is confirmed. Now the investigation shifts from what happened to who did it. Threat intelligence has been quietly pivoting on Ghost Lotus's infrastructure for days: domains, WHOIS records, aliases, and a formal cluster attribution report.

One alias keeps surfacing across everything, GL-01. Where it actually leads takes real pivoting, not a single lookup.

Evidence file: infrastructure.zip (domains.txt, whois.txt, aliases.txt, threat_report.pdf)

`SHA1: 0e7ff84e1fde82f6d8fba36eb299e42b1409daf4`

# CH5-OSINT-03 - Following The Trail

- **CTF:** Cyber Madness CTF 2026

- **Category:** OSINT

- **Points:** 850

- **Difficulty:** Hard

- **Status:** Solved ✅

## 📝 Description

> LotusCore's real value is confirmed. Now the investigation shifts from what happened to who did it. Threat intelligence has been quietly pivoting on Ghost Lotus's infrastructure for days: domains, WHOIS records, aliases, and a formal cluster attribution report.

---

## 🔍 Initial Analysis

The challenge provided multiple intelligence artifacts instead of a single source of truth. Rather than searching for the alias directly, the objective was to **pivot across multiple datasets** and correlate infrastructure, aliases, and threat intelligence.

The available files included:

- `domains.txt`

- `whois.txt`

- `aliases.txt`

- `threat_report.pdf`

The challenge hint ("not a single lookup") indicated that the solution required following an attribution chain instead of extracting a value from one file.

---

## 🛠️ Tools Used

- `cat`

- `grep`

- PDF Viewer

- Text Editor

- Manual OSINT correlation

---

## 🚀 Approach

The investigation started from the known alias **GL-01**.

Instead of assuming GL-01 itself was the answer, I followed every reference to the alias throughout the provided evidence.

The pivot chain looked like this:

```plain text
GL-01
    ↓
Aliases
    ↓
PGP / Identity Correlation
    ↓
WHOIS Records
    ↓
Infrastructure Ownership
    ↓
Threat Intelligence Report
    ↓
Final Attribution
```

The Threat Intelligence Report ultimately confirmed the identity behind the infrastructure cluster.

---

## 🏁 Solving Steps

1. Opened `aliases.txt` and searched for **GL-01**.

1. Correlated the alias with the associated identity and infrastructure references.

1. Compared the WHOIS information and registered domains.

1. Reviewed the attribution report inside `threat_report.pdf`.

1. The report identified the infrastructure cluster as belonging to **APT-Lotus**.

1. Submitted the flag.

---

## 🚩 Flag

```plain text
CM26{APT-Lotus}
```

---

## 💡 What I Learned

- OSINT investigations often rely on **pivoting**, not direct searching.

- WHOIS records, aliases, and infrastructure attribution become much more valuable when correlated together.

- Threat intelligence reports usually provide the final attribution after multiple pivots.

- Always read the challenge wording carefully—the question asked where **GL-01 leads**, not what GL-01 is.

---

## 🔗 References

- MITRE ATT&CK – Threat Groups

- Mandiant Threat Intelligence Methodology

- Recorded Future Intelligence Fundamentals

- WHOIS Documentation
