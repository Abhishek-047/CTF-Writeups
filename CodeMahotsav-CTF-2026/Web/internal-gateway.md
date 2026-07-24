## **CH3-WEB-03 - Internal Gateway**

### **450**

Created by Sujhal Gurav

3 (100% liked)  0

With LCORE-01 identified as the target, Ghost Lotus needed a way to actually reach it segmented networks don't cross themselves. An internal gateway service sits between the compromised zone and where they were headed. Enter your team name to begin, then find the access this gateway isn't supposed to grant.


`Portal: http://143.110.248.48:5003/`

## CH3-WEB-03 - Internal Gateway

- **CTF:** Event Name *(fill in your event name)*

- **Category:** Web

- **Points:** 450

- **Difficulty:** Medium

- **Status:** Solved ✅

### 📝 Description

> With LCORE-01 identified as the target, Ghost Lotus needed a way to actually reach it — segmented networks don't cross themselves. An internal gateway service sits between the compromised zone and where they were headed. Enter your team name to begin, then find the access this gateway isn't supposed to grant.

### 🔍 Initial Analysis

The portal served a Flask app (`Server: gunicorn`) hosted at `gateway.blacklotus.local`. The landing page only asked for a team name, hinting this was a gate before the "real" application. No obvious hints in page source, robots.txt, or common exposed files (`.env`, `.git/config` — all returned 404).

### 🛠️ Tools Used

- `curl` (macOS Terminal) for manual HTTP requests

- Browser (for exploring the "My Documents" page and links)

- Manual URL/ID enumeration (IDOR testing)

### 🚀 Approach

1. Started by mapping the flow of the app step by step rather than jumping straight to automated scanning — the challenge name ("Internal Gateway") and description strongly hinted at an access-control flaw, not something like SQLi or SSRF.

1. Submitted a team name to get past the initial gate, which set a Flask session cookie (`session=` — base64-encoded JSON containing `team_name`).

1. Was redirected to `/login`, an "Employee Gateway" requiring `username`/`password`.

1. Tried SQL injection (`' OR '1'='1`, `admin'--`) on the login form — no luck, ruling out that vector.

1. Went back to the challenge's own narrative/lore for credential hints, and successfully authenticated with `employee` / `Welcome@2026`.

1. After login, landed on a "My Documents" page with a downloadable file link containing a numeric ID in the URL.

1. Recognized this as a classic **IDOR (Insecure Direct Object Reference)** pattern — tested by manually changing the numeric ID in the URL.

### 🏁 Solving Steps

1. Visited `http://143.110.248.48:5003/`, submitted team name `Chole_Bhature`, received a session cookie via POST to `/`.

1. Was redirected to `/login`, presented with an Employee Gateway login form.

1. Authenticated using credentials `employee` / `Welcome@2026`.

1. Landed on a "My Documents" page containing a link to download a document, with the URL including a numeric document ID.

1. Manually brute-forced the ID by incrementing/changing the number in the URL.

1. At **ID 105**, the document returned was not meant for this account — an internal/privileged document exposing the flag directly (classic IDOR: the app checked authentication but not authorization/ownership per document).

1. Retrieved the flag from the document at ID 105.

### 🚩 Flag

`CM26{9dedaad6-idor_internal_access}`

### 💡 What I Learned

- Not every access-control challenge is SQLi or SSRF — always test the simplest/most direct path first (in this case, straightforward credential guessing based on challenge lore, followed by ID enumeration).

- IDOR vulnerabilities are often hiding behind seemingly-authenticated features — an app can correctly check "are you logged in?" while completely failing to check "should *you* see *this specific* resource?"

- Numeric, sequential resource IDs in URLs are a strong signal to test IDOR manually, even without automated tools — just increment/decrement and observe.

- Session cookies (Flask `session=`) are worth decoding early; they reveal what state the server is tracking about you, which can hint at what to tamper with.

### 🔗 References

- [OWASP: Insecure Direct Object References (IDOR)](https://owasp.org/www-community/attacks/Insecure_Direct_Object_Reference)

- [PortSwigger: Access Control Vulnerabilities](https://portswigger.net/web-security/access-control)
