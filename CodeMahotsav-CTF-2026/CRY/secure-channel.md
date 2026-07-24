## **CH3-CRY-03 - Secure Channel**

### **500**

Created by Sujhal Gurav

2 (100% liked)  0

With internal access expanded, Ghost Lotus opened a private channel to coordinate the next move encrypted, and this time clearly meant to keep the target itself secret from anyone who wasn't already read in.

Recover the message and confirm what Ghost Lotus was actually being told to go after.

Evidence file: channel.enc (plus notes.txt)

`SHA1: 9d34b6a63dedb26b37f418e729562fdc14272b47`

# CH3-CRY-03 - Secure Channel

- **CTF:** Cyber Madness CTF 2026

- **Category:** Cryptography

- **Points:** 500

- **Difficulty:** Medium

- **Status:** Solved ✅

## 📝 Description

> With internal access expanded, Ghost Lotus opened a private channel to coordinate the next move encrypted, and this time clearly meant to keep the target itself secret from anyone who wasn't already read in.

---

## 🔍 Initial Analysis

The challenge provided two files:

- `channel.enc`

- `notes.txt`

Opening `notes.txt` revealed several important hints:

- The message was protected using **multiple layers**.

- The first layer had to be removed before the second.

- The decryption key was related to the **Wave 1 Pastebin challenge**.

- The note mentioned *"1940s dance history"* and *"the first key opened the second door,"* pointing back to an earlier challenge rather than introducing a new key.

The encrypted file itself appeared to be Base64 encoded.

---

## 🛠️ Tools Used

- CyberChef

- Python 3

- Base64 Decoder

- Previous Wave 1 challenge artifacts

---

## 🚀 Approach

The first step was identifying the outer encoding. Decoding the file as Base64 produced another encrypted message rather than readable plaintext:

```plain text
Uicrft kzrdjzxpe. Pzzgcfl WNPRM01.
```

At this point, the hints from `notes.txt` became crucial. The reference to the **Wave 1 Pastebin challenge** indicated that the previously recovered value **GhostLotus_STAGE-2** was not another flag to submit, but instead the decryption key for this second encryption layer.

Several classical ciphers were considered (Vigenère, Beaufort, Autokey, etc.) using normalized forms of the recovered key. Applying the correct key successfully revealed the operator's command, instructing Ghost Lotus to proceed against **LCORE01**.

---

## 🏁 Solving Steps

1. Opened `channel.enc` and identified it as Base64-encoded.

1. Decoded the file to obtain:

1. Examined `notes.txt` for clues.

1. Recognized that the note referenced the earlier **Wave 1 Pastebin** challenge.

1. Used the previously recovered value **GhostLotus_STAGE-2** as the decryption key for the second encryption layer.

1. Decrypted the remaining ciphertext to recover the operator's instruction:

1. Converted the recovered message into the required flag format.

---

## 🚩 Flag

```plain text
CM26{Proceed_LCORE01}
```

---

## 💡 What I Learned

- Always read challenge notes carefully—they often contain the most valuable hints.

- Multi-layer encryption frequently combines simple encodings (such as Base64) with classical ciphers.

- CTF challenges often build on previous stages, requiring keys or artifacts recovered earlier in the event.

- Reusing intelligence gathered from earlier challenges can be just as important as solving the current one.

---

## 🔗 References

- [https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)

- [https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)

- [https://en.wikipedia.org/wiki/Base64](https://en.wikipedia.org/wiki/Base64)

---

# Cyber Madness CTF 2026

- **Organizer:** CYBER MADNESS

- **Rank:** #10

- **Dates:** July 2026

## 🏆 Summary

Cyber Madness CTF 2026 featured a connected storyline following the activities of the Ghost Lotus threat group. Challenges ranged across Web, Cryptography, Forensics, Networking, Reverse Engineering, and OSINT, with each stage revealing another piece of the campaign. This challenge demonstrated how earlier discoveries can become the key to solving later encrypted communications.

---

## 🚩 Challenges

---

## 📓 Quick Notes

- Decode every encoding layer before attempting cryptanalysis.

- Carefully inspect hint files—they often reference previous challenges.

- Preserve recovered keys from earlier stages; they may be reused later.

- Verify decrypted output before attempting flag submissions.

---

## 🧠 Key Learnings

- Identifying layered encryption workflows.

- Using contextual clues to determine decryption keys.

- Correlating artifacts across multiple CTF challenges.

- Applying a structured approach to cryptographic analysis rather than relying on guesswork.
