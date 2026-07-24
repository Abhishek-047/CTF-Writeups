## **CH2-OSINT-02 - Internal Recon**

### **200**

Created by Sujhal Gurav

2 (100% liked)  0

Not every clue comes from breaking in sometimes it's sitting in a spreadsheet nobody locked down properly. An internal employee directory was recovered. Somewhere in it, Black Lotus Technologies' R&D division is running several active projects under codenames most of them unremarkable. One of them isn't. Cross-reference what's visible. Then check what isn't. Evidence file: employee_directory.zip

`SHA1: 5b73009c00423a00c4a337d06f980eed5b526403`



```plain text
# Internal Recon
- **CTF:** CyberMahotsav 2026
- **Category:** OSINT
- **Points:** 200
- **Difficulty:** Easy
- **Status:** Solved ✅

## 📝 Description
> Not every clue comes from breaking in—sometimes it's sitting in a spreadsheet nobody locked down properly. An internal employee directory was recovered. Somewhere in it, Black Lotus Technologies' R&D division is running several active projects under codenames. Most of them are unremarkable. One of them isn't. Cross-reference what's visible. Then check what isn't.

**Evidence File:** `employee_directory.zip`

---

## 🔍 Initial Analysis
The provided ZIP archive contained an internal employee directory with multiple spreadsheet sheets and supporting notes. At first glance, everything appeared to be a normal employee database with department information and project assignments.

Since the challenge hinted to **"check what isn't visible"**, the spreadsheet was inspected for hidden worksheets and metadata instead of only reviewing the visible data.

---

## 🛠️ Tools Used
- LibreOffice Calc / Microsoft Excel
- Spreadsheet Hidden Sheet Inspector
- ZIP Archive Viewer

---

## 🚀 Approach
The challenge was clearly focused on information disclosure through improperly secured office documents.

Instead of searching only through the visible employee records, the spreadsheet structure was inspected for:
- Hidden worksheets
- Hidden rows/columns
- Cell comments
- Internal notes

A hidden worksheet named `_notes_do_not_share` was discovered containing confidential internal observations about R&D projects.

---

## 🏁 Solving Steps
1. Extracted the provided `employee_directory.zip`.
2. Opened the employee directory spreadsheet.
3. Reviewed the visible worksheets containing employee and project information.
4. Checked for hidden sheets inside the workbook.
5. Found the hidden worksheet named `_notes_do_not_share`.
6. Read the internal notes mentioning that among the active R&D codenames, **LotusCore** was the only project referenced in external intelligence and considered an adversary target.
7. Used the identified codename as the challenge flag.

---

## 🚩 Flag
`CM26{LotusCore}`

---

## 💡 What I Learned
- Office documents can unintentionally expose sensitive information through hidden worksheets.
- Always inspect workbook metadata instead of relying only on visible sheets.
- Hidden sheets, comments, and document properties are common sources of information leakage during OSINT investigations.
- Metadata analysis is an important part of document-based reconnaissance.

---

## 🔗 References
- https://owasp.org/www-community/attacks/Information_Leakage
- https://learn.microsoft.com/en-us/office/troubleshoot/excel/hide-or-display-worksheets
```

---

```plain text
# CyberMahotsav 2026
- **Organizer:** CyberMahotsav Team
- **Rank:** -
- **Dates:** 2026

## 🏆 Summary
Solved the **Internal Recon** OSINT challenge by analyzing an internal employee directory spreadsheet. The solution required inspecting hidden workbook content rather than only the visible employee data, demonstrating how sensitive information can remain exposed inside office documents.

## 🚩 Challenges

| # | Challenge | Category | Points | Difficulty | Status | Writeup |
| :- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Internal Recon | OSINT | 200 | Easy | Solved | ✅ |

## 📓 Quick Notes
- Always inspect hidden worksheets.
- Check workbook metadata and document properties.
- Review comments, notes, and hidden rows/columns.
- Information leakage often occurs through forgotten internal documentation.

## 🧠 Key Learnings
- Hidden Excel worksheets can contain confidential information.
- Metadata inspection is a critical OSINT skill.
- Never assume visible spreadsheet data is the complete dataset.
- Simple document misconfigurations can reveal high-value intelligence.
```
