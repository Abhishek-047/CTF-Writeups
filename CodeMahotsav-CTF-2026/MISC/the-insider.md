## **CH2-MISC-01 - The Insider**

### **300**

Created by Sujhal Gurav

0  0

Everything so far points outward phishing, external infrastructure, an encrypted note meant for someone on the inside. That last part is the problem.

Access logs, badge scans, and internal meeting records were pulled for the relevant window. Someone with legitimate access was doing things that don't line up with where they were supposed to be.

Cross-reference the three sources. Find who doesn't add up.

Evidence file: the_insider.zip (access_logs.csv, badge_scans.csv, meeting_records.pdf)



# Challenge Name

- **CTF:** Cyber Maze 2026

- **Category:** Misc

- **Points:** 300

- **Difficulty:** Medium

- **Status:** Solved ✅

## 📝 Description

> Everything so far points outward phishing, external infrastructure, an encrypted note meant for someone on the inside. That last part is the problem.

---

## 🔍 Initial Analysis

The challenge provided three different sources of evidence instead of a single file. The objective clearly hinted that no single file contained the answer; the solution required correlating information across all three datasets.

Files included:

- `access_logs.csv`

- `badge_scans.csv`

- `meeting_records.pdf`

The keyword *cross-reference* suggested looking for inconsistencies between:

- System logins

- Physical building access

- Official meeting records

---

## 🛠️ Tools Used

- VS Code

- Excel / LibreOffice Calc

- PDF Viewer

- Manual log analysis

---

## 🚀 Approach

I first reviewed each evidence file individually to understand what information it contained.

- The **meeting records** described employees, access approvals, and internal discussions.

- The **badge scan logs** recorded physical entry into various buildings.

- The **access logs** contained VPN and file server authentication records.

The goal was to identify an employee whose digital activity did not match their documented responsibilities or physical presence.

---

## 🏁 Solving Steps

1. Opened `meeting_records.pdf` and reviewed the employee access discussions.

1. Noticed a temporary access approval for **Amit Verma** to access **FILE-SERVER-02**.

1. Continued reading the meeting notes and found that the supposed migration task was later identified as invalid.

1. Opened `access_logs.csv` and filtered entries related to `FILE-SERVER-02`.

1. Found multiple late-night logins associated with Amit Verma, including VPN access from an external IP address.

1. Cross-checked `badge_scans.csv`.

1. Observed badge activity that did not align with the times and locations expected for the approved work.

1. Combining all three sources identified **Amit Verma** as the insider.

---

## 🚩 Flag

```plain text
CM26{Amit_Verma}
```

---

## 💡 What I Learned

- Cross-referencing multiple evidence sources is often more important than analyzing a single log.

- Correlation between physical access and system activity can expose insider threats.

- Temporary privilege escalation should always be audited carefully.

- Meeting notes may contain subtle clues that explain suspicious log entries.

---

## 🔗 References

- MITRE ATT&CK – Insider Threat

- Blue Team log correlation techniques

- Windows Event Log analysis
