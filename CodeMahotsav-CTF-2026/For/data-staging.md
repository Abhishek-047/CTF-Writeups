## **CH4-FOR-04 - Data Staging**

### **650**

Created by Sujhal Gurav

0  0

With admin access to LCORE-01, Ghost Lotus stopped reconnaissance and started preparing to move data out. A filesystem image was pulled from the host before it could be wiped. Somewhere in this filesystem, a staging archive holds the actual target mixed in with decoys, disguised under a generic filename. Log correlation is the only way to tell them apart.

Evidence file: filesystem.tar.gz

`SHA1: c932bea8ffd51a600ff1eea1274d624f369c991b`

# CH4-FOR-04 - Data Staging

- **CTF:** Ghost Lotus (CH4-FOR series)

- **Category:** Forensics

- **Points:** 650

- **Difficulty:** Hard

- **Status:** Solved ✅

## 📝 Description

> With admin access to LCORE-01, Ghost Lotus stopped reconnaissance and started preparing to move data out. A filesystem image was pulled from the host before it could be wiped. Somewhere in this filesystem, a staging archive holds the actual target mixed in with decoys, disguised under a generic filename. Log correlation is the only way to tell them apart.

## 🔍 Initial Analysis

Extracted the tarball and verified its SHA1 against the provided hash before touching anything. The filesystem image contained a small, deliberately curated set of artifacts:

```plain text
archive/lotuscore_stage.zip
logs/archive_manifest.log
logs/auth.log
logs/security_alert.log
temp/.~zip_9f3ac2.tmp
users/raj.patel/notes.txt
```

`archive/lotuscore_stage.zip` contained four files with intentionally generic names — `data_01.db` … `data_04.db` — each an 8,192‑byte SQLite database. Nothing in the zip itself (filenames, comments, extra fields) hinted at their real identity, confirming the description's claim that the target was "disguised under a generic filename."

## 🛠️ Tools Used

- `tar`, `unzip` — evidence extraction

- `sha256sum` — hash correlation

- Python `sqlite3` / `zipfile` / `struct` — parsing DB content and SQLite headers, inspecting zip metadata

- `strings`, `od` / hex inspection — checking for embedded/hidden text and stego

- Manual log correlation across `auth.log`, `security_alert.log`, and `archive_manifest.log`

## 🚀 Approach

The description explicitly states "log correlation is the only way to tell them apart," so the working theory from the start was:

1. The 4 generically-named files in the zip are real copies of known files — I just need to recover their original identities via hashing.

1. Once identities are recovered, one of them is the genuine exfil target and the rest are decoys — some signal in the logs has to separate them, since content/hashes alone made all four look equally "legitimate."

I initially considered that the real target might be the file *without* a clean hash match (something tampered with), but all four staged files hashed identically to known-good entries — ruling that out and pointing instead toward a monitoring/behavioral signal rather than a content/integrity signal.

## 🏁 Solving Steps

1. **Verify evidence integrity** — confirmed `sha1sum filesystem.tar.gz` matched the provided hash before analysis.

1. **Extract and hash the staged files:**

1. **Cross-reference against **`**archive_manifest.log**` (a backup-verification log listing filename ↔ SHA256 pairs). Four of the nine listed entries matched exactly:

1. **Distinguish target from decoys via **`**security_alert.log**`**.** Only one of the four hashes appeared in a File Integrity Monitoring alert:

1. **Corroborated with a second artifact.** `temp/.~zip_9f3ac2.tmp` — a truncated, orphaned SQLite file disguised as generic zip working cruft — was compared byte-for-byte against all four staged files. It matched the first 2,200 bytes of `data_03.db` exactly and nothing else, confirming this specific file was independently being copied/handled outside the finished archive.

1. **Correlated with **`**auth.log**` to anchor the timeline: `rpatel` escalated to root, ran `find . -newer /tmp/.marker` in `/srv/projects/lotuscore/data`, then ran `zip -j /srv/staging/archive/lotuscore_stage.zip *` — matching the FIM alert timestamps to the second.

1. **Conclusion:** `data_03.db` is `lotuscore_research.db` — the genuine exfiltration target, singled out by monitoring classification, timing, and a duplicate handling artifact, while the other three staged files are legitimate-looking decoys bundled in to blend the archive in with routine backup traffic.

## 🚩 Flag

`CM26{lotuscore_research}`

## 💡 What I Learned

- When every candidate file hashes "clean" against a known-good manifest, integrity checking alone isn't enough — the real differentiator can be *behavioral* (access timing, monitoring tier, change-ticket linkage) rather than content-based.

- Orphaned/temp artifacts (`.~zip_*.tmp`style files) are worth diffing against your primary evidence byte-for-byte; they can independently corroborate which object was actually being manipulated, even when the file itself looks empty or corrupted at first glance.

- Always sanity-check log entries for filler/decorative data (e.g., obviously patterned hex hashes) before trusting them as real correlation material — noise is often deliberately mixed in with signal.

## 🔗 References

- SQLite file format spec (for manually parsing the header of the truncated temp DB): https://www.sqlite.org/fileformat2.html
