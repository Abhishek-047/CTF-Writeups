## **CH2-WEB-02 Forgotten Dashboard**

### **250**

Created by Sujhal Gurav

1 (100% liked)  0

Not every internal tool gets decommissioned properly. Somewhere in Black Lotus Technologies' infrastructure sits a dashboard nobody's thought about in years — old enough that it's not on anyone's asset list anymore, current enough that it's still reachable. Ghost Lotus found it before your team did. Enter your team name to begin, then find what's still exposed.

`Portal: http://143.110.248.48:5002/`



# Forgotten Dashboard

- **CTF:** CM26

- **Category:** Web Exploitation

- **Points:** 250

- **Difficulty:** Easy

- **Status:** Solved ✅

## 📝 Description

> Not every internal tool gets decommissioned properly. Somewhere in Black Lotus Technologies' infrastructure sits a dashboard nobody's thought about in years — old enough that it's not on anyone's asset list anymore, current enough that it's still reachable. Ghost Lotus found it before your team did. Enter your team name to begin, then find what's still exposed.

---

## 🔍 Initial Analysis

Visiting the challenge URL displayed a simple internal portal named **LotusCore Internal Portal** with a single input field requesting a team name.

The HTML source revealed:

- A basic HTML form using the **POST** method.

- No client-side JavaScript.

- No obvious hidden comments or endpoints.

- No visible flag within the page source.

Since the application requested a team name, the next logical step was to inspect the HTTP response generated after submitting the form.

---

## 🛠️ Tools Used

- `curl`

- macOS Terminal

---

## 🚀 Approach

Instead of relying on a browser, I interacted with the application directly using `curl` to inspect raw HTTP requests and responses.

The objective was to determine:

- Whether submitting the form caused a redirect.

- Whether cookies or session tokens contained sensitive information.

- Whether the dashboard exposed additional data.

---

## 🏁 Solving Steps

### 1. Inspect the landing page

The homepage was retrieved using:

```bash
curl -i http://143.110.248.48:5002/
```

The response confirmed a simple HTML form:

```html
<form method="post">
    <input type="text" name="team_name">
</form>
```

---

### 2. Submit a team name

The form was submitted using:

```bash
curl -i -X POST \
-d "team_name=Chole_Bhature" \
http://143.110.248.48:5002/
```

The server responded with:

```plain text
HTTP/1.1 302 FOUND
Location: /dashboard
Set-Cookie: session=...
```

The important observation was the **Set-Cookie** header.

---

### 3. Inspect the session cookie

The session cookie looked similar to:

```plain text
session=eyJ0ZWFtX25hbWUiOiJDaG9sZV9CaGF0dXJlIiwiZmxhZyI6IkNNMjZ7NmYwYTQzMjgtUk5ELTA0fSJ9...
```

The first section before the first `.` resembled a Base64URL-encoded payload.

After decoding it, the JSON payload became:

```json
{
    "team_name": "Chole_Bhature",
    "flag": "CM26{6f0a4328-RND-04}"
}
```

The application was storing the flag directly inside the client-side session cookie.

---

### 4. Retrieve the flag

Since the flag was embedded inside the session payload, no additional exploitation was required.

The challenge was solved by extracting the decoded value.

---

## 🚩 Flag

```plain text
CM26{6f0a4328-RND-04}
```

---

## 💡 What I Learned

- Always inspect HTTP response headers.

- Session cookies may leak sensitive information.

- Many Flask applications use signed client-side sessions rather than encrypted sessions.

- Base64URL-encoded values are worth decoding during web challenges.

- Simple tools like `curl` are often sufficient to solve introductory web CTF challenges without using Burp Suite.

---

## 🔗 References

- Flask Secure Cookie Sessions

- Base64URL Encoding

- OWASP Session Management Cheat Sheet

# CM26

- **Organizer:** Black Lotus Technologies

- **Rank:** *To Be Updated*

- **Dates:** *To Be Updated*

## 🏆 Summary

CM26 featured challenges across multiple cybersecurity domains including Web Exploitation, OSINT, Cryptography, Digital Forensics, Reverse Engineering, and Miscellaneous categories. The event focused on practical attack techniques, investigative workflows, and realistic security scenarios inspired by enterprise environments.

---

## 🚩 Challenges

---

## 📓 Quick Notes

- Inspect every HTTP response header.

- Decode cookies, JWTs, and Base64-encoded values.

- Follow redirects carefully.

- Don't ignore `Set-Cookie` headers.

- `curl` is often enough for solving basic web challenges.

- Client-side sessions may accidentally expose sensitive information.

---

## 🧠 Key Learnings

- HTTP request and response analysis

- Session cookie inspection

- Base64URL decoding

- Flask client-side session handling

- Information disclosure vulnerabilities

- Using `curl` effectively for web enumeration
