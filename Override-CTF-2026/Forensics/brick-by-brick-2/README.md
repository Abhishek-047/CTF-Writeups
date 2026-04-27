# Brick By Brick - 2

## Overview
Writeup for **Brick By Brick - 2** from Override CTF 2026.

## Category
Steganography / Forensics

## Difficulty
Hard

## Description
The challenge provided a JPEG image named `challenge.jpg`. The hint suggested that the image was only the beginning of a deeper puzzle: *"Some fragments are hidden, others are broken, and none make sense on their own."* This was a multi-layered "rabbit hole" challenge involving metadata analysis, steganography, and archive repair.

## Initial Analysis
Initial inspection of the file revealed a normal JPEG, but the `file` command and hex inspection showed a suspicious comment:
```text
"dGhlIHBhc3MgbGllcyBpbiBwYXJ0IDEgZmxhZw=="
```
Decoding this Base64 string revealed: `the pass lies in part 1 flag`. This confirmed that the password for the next stage was the flag/key from the previous challenge (**Brick By Brick - 1**), which was `m4n713`.

## Tools Used
- `file` / `exiftool` (Metadata inspection)
- `steghide` (Data extraction)
- `base64` (Hint decoding)
- `zip` (`-FF` for archive repair)

## Approach
1. **Metadata Recovery:** Decode the Base64 comment to find the passphrase for steganography tools.
2. **Steganographic Extraction:** Use `steghide` with the recovered passphrase to extract hidden data from the JPEG.
3. **Archive Analysis:** Inspect the extracted ZIP and identify secondary "broken" fragments.
4. **Archive Repair:** Use Zip's "fix" utility to reconstruct the corrupted internal archive.
5. **Final Extraction:** Retrieve the flag from the repaired data structure.

## Steps to Solve
### 1. Extracting the First Layer
Using the passphrase `m4n713` (from Part 1), I used `steghide` to extract the embedded payload:
```bash
steghide extract -sf challenge.jpg -p m4n713
```
This produced a file named `secret.zip`.

### 2. Inspecting the Archive
The `secret.zip` contained:
- `flag.jpg` (A decoy/distraction)
- `information.txt` (Hint: *"Some things appear broken... but aren't."*)
- `kit.001` (A mysterious file)

### 3. Repairing the Broken Fragment
Running `file kit.001` identified it as "Zip archive data," but standard unzip commands failed due to a missing "End-of-central-directory" signature. Following the hint, I used `zip -FF` to repair the archive:
```bash
zip -FF kit.001 --out fixed.zip
```
During the repair process, the utility successfully identified and recovered a directory structure: `final/flag.txt`.

### 4. Retrieving the Flag
Unzipping the newly created `fixed.zip` revealed the final flag:
```bash
unzip fixed.zip
cat final/flag.txt
```

## Flag
`OVRD{d33p_1n70_r4bb17_h0l3_766f6964}`

## What I Learned
- **Cross-Challenge Dependencies:** How CTF designers link multiple challenges using shared keys or hints.
- **Archive Forensics:** Recognizing that `.001` files or "broken" headers often require manual or automated repair tools like `zip -FF` or `binwalk`.
- **Multi-Layer Steganography:** Managing multiple stages of data extraction where each successful step leads to a new type of forensic problem.

## References
_N/A_
