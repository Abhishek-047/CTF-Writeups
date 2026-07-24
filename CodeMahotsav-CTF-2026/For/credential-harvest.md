## **CH3-FOR-03 - Credential Harvest**

### **350**

Created by Sujhal Gurav

2 (100% liked)  0

With a foothold established and an insider possibly cooperating, Ghost Lotus stopped being subtle. Authentication logs across multiple internal systems show a pattern of credential harvesting not one account, several, pulled together over a short window.

Auth logs, Kerberos logs, VPN logs, and an asset inventory were recovered from the affected systems. Somewhere in the noise is a service account that should never have been usable this way.

Evidence file: auth_logs.zip (auth_audit.log, kerberos.log, vpn.log, asset_inventory.csv)

`SHA1: ce827a5c7f399af0e27064a204e0986306c2b710`

# CH3-FOR-03 - Credential Harvest

- **CTF:** Cyber Madness CTF 2026

- **Category:** Digital Forensics

- **Points:** 350

- **Difficulty:** Medium

- **Status:** Solved ✅

---

## 📝 Description

> With a foothold established and an insider possibly cooperating, Ghost Lotus stopped being subtle. Authentication logs across multiple internal systems show a pattern of credential harvesting—not one account, several, pulled together over a short window.

---

## 🔍 Initial Analysis

The provided ZIP archive contained multiple authentication-related log files representing different parts of the enterprise infrastructure.

The challenge description hinted that:

- Multiple credentials were harvested.

- A **service account** was abused.

- The objective was to identify the account that **should never have been used interactively**.

The investigation focused on correlating:

- Windows authentication events

- Kerberos ticket requests

- VPN access logs

- Asset inventory

---

## 🛠️ Tools Used

- Kali Linux

- `grep`

- `less`

- `sort`

- `awk`

- Manual log correlation

---

## 🚀 Approach

The investigation began by reviewing authentication logs for unusual login behavior.

Normal user accounts generated expected interactive logins, while service accounts typically authenticated using **Service (Logon Type 5)** or **Batch (Logon Type 4)** logons.

One account immediately stood out:

- `svc-backup`

Instead of only performing scheduled service operations, it suddenly generated **RemoteInteractive (Logon Type 10)** logins.

To verify the compromise:

1. Correlated authentication logs with Kerberos ticket requests.

1. Checked VPN activity around the same timeframe.

1. Mapped the involved workstation using the asset inventory.

1. Confirmed the service account was later used for lateral movement.

---

## 🏁 Solving Steps

1. Extracted the provided `auth_logs.zip` archive.

1. Reviewed authentication logs for abnormal login types.

1. Identified `svc-backup` performing **RemoteInteractive (Logon Type 10)** logins, which is highly unusual for a service account.

1. Correlated the timestamps with `kerberos.log`, where the same account requested tickets for:

1. Reviewed `vpn.log` and observed a suspicious VPN login from IP **185.220.101.47** associated with **Karan Mehta**, indicating the attacker likely compromised that workstation first.

1. Using the asset inventory and timeline, concluded that the harvested service account abused for lateral movement was `**svc-backup**`.

---

## 🚩 Flag

```plain text
CM26{svc-backup}
```

---

## 💡 What I Learned

- Service accounts should never perform interactive or Remote Desktop logins.

- Correlating authentication logs with Kerberos activity provides strong evidence of lateral movement.

- Logon Types are valuable indicators during Windows forensic investigations:

- Combining multiple log sources helps reconstruct an attack timeline with high confidence.

---

## 🔗 References

- Microsoft Windows Security Logon Types

- MITRE ATT&CK – T1078 (Valid Accounts)

- MITRE ATT&CK – T1558 (Steal or Forge Kerberos Tickets)

---

# Cyber Madness CTF 2026

- **Organizer:** Cyber Madness

- **Rank:** N/A

- **Dates:** 2026

---

## 🏆 Summary

CH3-FOR-03 focused on forensic timeline analysis by correlating authentication, Kerberos, VPN, and asset inventory logs. Rather than searching for a hidden string, the challenge required identifying abnormal authentication behavior and recognizing that a service account was improperly used for interactive logins, revealing the compromised credentials used during the attack.

---

## 🚩 Challenges

---

## 📓 Quick Notes

- Always examine Windows Logon Types before investigating account activity.

- Service accounts should only perform service or batch logons.

- Correlate authentication events with Kerberos tickets to detect lateral movement.

- VPN logs can help identify the initial compromised user or endpoint.

- Building a timeline across multiple log sources is often the fastest way to solve enterprise forensic challenges.

---

## 🧠 Key Learnings

- Windows authentication log analysis

- Kerberos ticket correlation

- VPN log investigation

- Service account abuse detection

- Lateral movement identification

- Timeline-based forensic analysis
