## **CH1-FOR-01 - Ghost Lotus Campaign**

### **150**

Created by Sujhal Gurav

4 (100% liked)  0

While reviewing outbound communications, the team found a suspicious email campaign targeting multiple employees. The messages look ordinary, but something feels off. Hidden within is a clue that could explain how the attack began. Your mission: Analyze the evidence and uncover the hidden intent behind the campaign. Inspect the email and its headers. Identify suspicious indicators — sender anomalies, link redirects, hidden content, attachments. Extract the hidden clue that reveals the true nature of this campaign.

`Portal: http://143.110.248.48:8081/`

# Ghost Lotus Campaign

- **CTF:** Cyber Madness CTF 2026

- **Category:** Forensics

- **Points:** 150

- **Difficulty:** Easy

- **Status:** Solved ✅

## 📝 Description

> While reviewing outbound communications, the team found a suspicious email campaign targeting multiple employees. The messages look ordinary, but something feels off.

## 🔍 Initial Analysis

The challenge portal provided a ZIP archive containing the email evidence. After extracting the archive, I found a Microsoft Word document. Since phishing documents commonly abuse Office macros, I decided to inspect the document's macros instead of just reading its visible content.

## 🛠️ Tools Used

- Web Browser

- Microsoft Word

## 🚀 Approach

Rather than analyzing the document's visible text, I inspected its VBA Macros. The macro editor referenced an additional PowerShell script stored in a recovered directory, suggesting that the document was intended to download or execute a second-stage payload.

## 🏁 Solving Steps

1. Opened the challenge portal:

1. Downloaded the provided ZIP archive.

1. Extracted the ZIP and opened the Microsoft Word document.

1. In Microsoft Word, navigated to:

1. Inside the macro editor, found a reference to:

1. Since the challenge portal was hosted at:

1. Opening the PowerShell script revealed:

1. Combining the three flag components produced the final flag.

## 🚩 Flag

```plain text
CM26{phishing_vector_confirmed}
```

## 💡 What I Learned

- Office documents can contain hidden VBA macros that reveal attacker behavior.

- Macros may reference additional payloads or scripts stored elsewhere.

- When analyzing phishing documents, inspect both the document contents and any embedded macro code.

- Hidden scripts or second-stage payloads often contain valuable forensic evidence.

## 🔗 References

- [https://learn.microsoft.com/en-us/office/vba/library-reference/concepts/getting-started-with-vba-in-office](https://learn.microsoft.com/en-us/office/vba/library-reference/concepts/getting-started-with-vba-in-office)

- [https://attack.mitre.org/techniques/T1059/001/](https://attack.mitre.org/techniques/T1059/001/)

- [https://attack.mitre.org/techniques/T1566/](https://attack.mitre.org/techniques/T1566/)
