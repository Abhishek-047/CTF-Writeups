CH4-MISC-03 - The Real Secret
800
Created by Sujhal Gurav
00
The directive is recovered extract, destroy traces, exit immediately. What's still missing is why. An emergency board session was called once the scope of LotusCore became clear to leadership for the first time.
Three documents were circulated for that meeting. None of them state the full picture in one place and at least one of them isn't showing you everything it looks like it's showing you.
Evidence file: executive_archive.zip (board_minutes.pdf, strategy.pptx, classification.docx)

```plain text
SHA1: 3effc1146d3ab7a27e3a6e46bcf0719cd84da03d
```

# CH4-MISC-03 - The Real Secret

- **CTF:** Event Name

- **Category:** Misc

- **Points:** 800

- **Difficulty:** Hard

- **Status:** Solved ✅

## 📝 Description

> The directive is recovered extract, destroy traces, exit immediately. What's still missing is why. An emergency board session was called once the scope of LotusCore became clear to leadership for the first time.

## 🔍 Initial Analysis

Unzipping the archive gave three normal-looking corporate documents: a PDF of board minutes, a PPTX strategy deck, and a DOCX classification memo. Each rendered as a clean, plausible-looking Office document with no obvious redaction or corruption — the challenge title and prompt ("isn't showing you everything it looks like it's showing you") signaled that the interesting content wouldn't be visible in a normal viewer, and would instead be hiding in the underlying XML/OOXML structure of each file.

## 🛠️ Tools Used

- `unzip` (OOXML files are just zip containers)

- `pdfplumber` (Python) for PDF text/metadata extraction

- Manual inspection of raw XML (`document.xml`, `presentation.xml`, `slideN.xml`, `notesSlideN.xml`, `docProps/app.xml`, `docProps/core.xml`)

- `grep` for structural markers (`w:vanish`, `show=`, relationship IDs)

## 🚀 Approach

Rather than trusting the rendered text of each document, I treated each file as a zip archive and read the raw OOXML underneath:

- For the **PDF**, I checked for hidden layers (OCG), annotations, and invisible text render modes — came up clean, so the PDF's contribution was just the plain visible text.

- For the **PPTX**, I compared the slide count claimed in `docProps/app.xml` metadata against the actual `<p:sldIdLst>` in `presentation.xml` — a classic tell for an inserted/hidden slide that wasn't accounted for when the deck's stats were last cached.

- For the **DOCX**, I searched for tracked changes, comments, and Word's hidden-text run property (`<w:vanish/>`), since visually the memo looked complete and "final."

The theory quickly became: each document *individually* shows a partial, sanitized version of the truth, and the full picture (LotusCore isn't just an internal facilities system — it's embedded in third-party critical infrastructure) only emerges by combining the hidden fragments from all three.

## 🏁 Solving Steps

1. Extracted `board_minutes.pdf`, `strategy.pptx`, and `classification.docx` from the zip.

1. Parsed `board_minutes.pdf` with `pdfplumber` — found the CISO's line that LotusCore "powers national‑scale INDUSTRIAL control networks, not just our own facilities" (deliberately capitalized "INDUSTRIAL"). No hidden layers/annotations in the PDF itself.

1. Unzipped `strategy.pptx` and diffed `docProps/app.xml` (claims 5 slides) against `ppt/presentation.xml`'s `<p:sldIdLst>` (actually lists 6 slide IDs, with `rId12` → `slides/slide6.xml` inserted out of sequence).

1. Opened `slide6.xml` directly and found `show="0"` — a **PowerPoint-hidden slide** titled "LotusCore — True Scope (Restricted)," stating LotusCore is "real‑time AUTOMATION of third‑party control systems," deployed inside "partner utility, transit, and manufacturing operators," and explicitly noting the slide is "excluded from all external and board‑level decks by design."

1. Checked the speaker notes (`notesSlide3.xml`) — found an instruction to avoid presenting the historical-context slide (2019 pilot → 2021 consolidation → 2023 charter) unless directly pressed.

1. Unzipped `classification.docx` and grepped `word/document.xml` for `w:vanish` — found a hidden (invisible-on-render) paragraph appended after the visible memo text: an "Audit rationale" confirming LotusCore's control-plane extends into "national‑scale critical infrastructure operated by third parties," classified Tier‑1 as a national security event.

1. Synthesized all three fragments — none of the three documents alone states the complete picture (each hides or omits part of it), but together they confirm LotusCore is an industrial automation platform that reaches into third-party critical infrastructure at national scale.

1. Constructed the flag from the two distinctly emphasized (capitalized) keywords planted across the two separate hidden pieces of evidence — "INDUSTRIAL" (PDF) and "AUTOMATION" (hidden PPTX slide) — and matched the checker's case-sensitive formatting.

## 🚩 Flag

`CM26{Industrial_Automation}`

## 💡 What I Learned

- OOXML files (docx/pptx/xlsx) are zip archives — always unzip and read the raw XML instead of trusting the rendered view when a challenge hints at hidden content.

- PowerPoint's `show="0"` attribute on a `<p:sld>` element marks a genuinely hidden slide that won't appear during a normal slideshow, and its slide ID can be inserted out of order in `<p:sldIdLst>` without updating the cached slide-count metadata in `docProps/app.xml` — a good place to spot inconsistencies.

- Word's `<w:vanish/>` run property hides text from view/print while leaving it fully intact and extractable in the XML — a common place to stash "for audit purposes only" content.

- Pay attention to deliberate, unusual emphasis (e.g., stray ALL‑CAPS words) in flavor text — it can double as a literal hint for flag casing/formatting, not just narrative flavor.

## 🔗 References

- [OOXML file format overview (Microsoft)](https://learn.microsoft.com/en-us/openspecs/office_standards/ms-oi29500/)

- [PowerPoint hidden slides (](https://ecma-international.org/publications-and-standards/standards/ecma-376/)[`show`](https://ecma-international.org/publications-and-standards/standards/ecma-376/)[ attribute) — ECMA-376 spec](https://ecma-international.org/publications-and-standards/standards/ecma-376/)

- [Word hidden text (](https://learn.microsoft.com/en-us/openspecs/office_standards/ms-oi29500/)[`w:vanish`](https://learn.microsoft.com/en-us/openspecs/office_standards/ms-oi29500/)[) — OOXML WordprocessingML reference](https://learn.microsoft.com/en-us/openspecs/office_standards/ms-oi29500/)
