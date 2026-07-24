## **CH5-FOR-05 - Operator Error**

### **900**

Created by Sujhal Gurav

0  0

APT-Lotus is attributed. Now find the person behind it. A forensic artifact survived on what's believed to be the operator's own workstation, recovered despite an attempt to wipe it clean.

Document metadata, system configuration, and an application cache all point somewhere. One more file in the collection wasn't supposed to be there at all.

Evidence file: operator_pc.zip (metadata.txt, timezone.log, editor_cache.db, and one more file)

`SHA1: 3eadc7a52a1d9199aa583bf7e113b9174bf49d1b`

# Operator Error

- **CTF:** Cyber Madness CTF 2026

- **Category:** Forensics

- **Points:** 900

- **Difficulty:** Hard

- **Status:** Solved ✅

## 📝 Description

> APT-Lotus is attributed. Now find the person behind it. A forensic artifact survived on what's believed to be the operator's own workstation, recovered despite an attempt to wipe it clean.

---

## 🔍 Initial Analysis

The provided archive contained four artifacts:

- `metadata.txt`

- `timezone.log`

- `editor_cache.db`

- `$IX7K2P9.thash`

The challenge description suggested that the operator's identity could be reconstructed by correlating evidence from multiple forensic artifacts rather than relying on a single file.

---

## 🛠️ Tools Used

- SQLite Browser (`DB Browser for SQLite`)

- `sqlite3`

- `strings`

- `cat`

- Hex Editor (optional)

---

## 🚀 Approach

I began by examining each artifact individually to determine whether any directly revealed the operator's identity.

- **metadata.txt** contained document metadata, including the document author.

- **timezone.log** revealed the workstation's timezone configuration and system details.

- **editor_cache.db** stored application cache entries, including recently opened workspaces and the editor username.

- **$IX7K2P9.thash** appeared to be an unexpected Windows artifact, exposing a deleted filename and directory structure.

Both the document metadata and editor cache consistently referenced the same Windows user account.

Initially, I assumed the flag would use the username exactly as recovered (`arjun.v`). However, the challenge expected a normalized version where the period (`.`) was replaced with an underscore (`_`).

---

## 🏁 Solving Steps

1. Extracted `operator_pc.zip`.

1. Inspected `metadata.txt` and identified the document author as:

1. Opened `editor_cache.db` using SQLite and found cache entries such as:

1. Examined the unexpected forensic artifact (`$IX7K2P9.thash`), which referenced:

1. Since every artifact pointed to the same username, the operator identity was determined to be **arjun.v**.

1. The platform required the username in flag format with the period replaced by an underscore.

---

## 🚩 Flag

```plain text
CM26{arjun_v}
```

---

## 💡 What I Learned

- User attribution often requires correlating multiple forensic artifacts instead of trusting a single source.

- SQLite databases frequently contain valuable application metadata such as usernames, workspaces, and recent activity.

- Windows forensic artifacts (including thumbnail/hash cache files) can reveal deleted filenames and user directories.

- Always consider challenge-specific flag formatting conventions (such as replacing special characters) before submitting.

---

## 🔗 References

- [https://www.sqlite.org/index.html](https://www.sqlite.org/index.html)

- [https://ericzimmerman.github.io/](https://ericzimmerman.github.io/)

- https://www.sans.org/white-papers/forensic-analysis-windows-artifacts/

- [https://book.hacktricks.wiki/en/generic-methodologies-and-resources/basic-forensic-methodology/index.html](https://book.hacktricks.wiki/en/generic-methodologies-and-resources/basic-forensic-methodology/index.html)
