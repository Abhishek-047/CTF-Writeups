## **CH1-WEB-01 - Hidden Employee Portal**

### **100**

Created by Sujhal gurav

4 (100% liked)  0

Black Lotus Technologies' internal employee portal has been live for years nobody's looked at it closely in a while. Recent activity suggests someone else has. Find what's exposed. Something on this portal shouldn't be reachable, and whatever's in it is the first thread in this whole investigation.

`Target: http://143.110.248.48:5004/`



```plain text
# Hidden Employee Portal
- **CTF:** Cyber Madness CTF 2026
- **Category:** Web
- **Points:** 100
- **Difficulty:** Easy
- **Status:** Solved ✅

## 📝 Description
> Black Lotus Technologies' internal employee portal has been live for years nobody's looked at it closely in a while. Recent activity suggests someone else has. Find what's exposed. Something on this portal shouldn't be reachable, and whatever's in it is the first thread in this whole investigation.
>
> **Target:** http://143.110.248.48:5004/

## 🔍 Initial Analysis
The target initially displayed a normal webpage with no obvious clues. A common first step in web enumeration is checking for a `robots.txt` file, as it sometimes reveals hidden or restricted directories.

Visiting `http://143.110.248.48:5004/robots.txt` returned:

```txt
User-agent: *
Disallow: /employee-portal
Disallow: /migration

# old migration paths still exist
```

This immediately suggested that the hidden directories were worth investigating.

## 🛠️ Tools Used
- Web Browser
- LLM (for initial reconnaissance suggestion)

## 🚀 Approach
Since the `robots.txt` file exposed two hidden paths, I manually explored both of them to look for sensitive information left behind during the migration process.

## 🏁 Solving Steps

1. Opened the target website:

   ```
   http://143.110.248.48:5004/
   ```

2. Checked the `robots.txt` file:

   ```
   http://143.110.248.48:5004/robots.txt
   ```

   which revealed:

   ```txt
   User-agent: *
   Disallow: /employee-portal
   Disallow: /migration

   # old migration paths still exist
   ```

3. Visited both hidden directories:

   ```
   http://143.110.248.48:5004/employee-portal
   ```

   and

   ```
   http://143.110.248.48:5004/migration
   ```

4. The `/migration` page listed migration-related files:

   ```txt
   Migration Status Files:
   - backup-notes.txt

   Pending Cleanup:
   - employee-portal
   ```

5. Opened the exposed backup file:

   ```
   http://143.110.248.48:5004/backup-notes.txt
   ```

   which contained:

   ```txt
   Migration Notes

   Old employee portal remains active.

   Temporary credentials:

   employee
   Welcome@2026

   Remove after migration.
   ```

6. Used the leaked credentials to log in to the employee portal:

   - **Username:** `employee`
   - **Password:** `Welcome@2026`

7. After successful authentication, the portal displayed the flag.

## 🚩 Flag

```text
CM26{portal_c1ffffb752846a}
```

## 💡 What I Learned
- Always check for a `robots.txt` file during web reconnaissance.
- Migration or backup directories often contain sensitive information that should never be publicly accessible.
- Temporary credentials left in backup or documentation files can lead to unauthorized access.
- Small information disclosures can quickly escalate into a full compromise.

## 🔗 References
- https://owasp.org/www-project-web-security-testing-guide/
- https://portswigger.net/web-security/information-disclosure
```

And for your event README:

```plain text
# Cyber Madness CTF 2026
- **Organizer:** Cyber Madness
- **Rank:** TBD
- **Dates:** TBD

## 🏆 Summary
Cyber Madness CTF 2026 featured challenges across multiple categories including Web, Cryptography, Forensics, OSINT, Reverse Engineering, and Miscellaneous. The event focused on practical attack techniques, encouraging participants to identify misconfigurations, analyze artifacts, and exploit real-world style vulnerabilities.

## 🚩 Challenges

| # | Challenge | Category | Points | Difficulty | Status | Writeup |
| :- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Hidden Employee Portal | Web | 100 | Easy | Solved ✅ | `writeups/web/hidden-employee-portal.md` |

## 📓 Quick Notes
- Check `robots.txt` during initial reconnaissance.
- Explore migration and backup directories.
- Look for exposed credentials in notes and configuration files.
- Never ignore information disclosure issues.

## 🧠 Key Learnings
- Information disclosure can be enough to compromise an application.
- Sensitive files should never remain publicly accessible after migrations.
- Good reconnaissance often solves easy web challenges without requiring exploitation.
- Following small clues methodically is often more effective than trying complex attacks.
```
