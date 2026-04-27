# Brick By Brick - 1

## Overview
Writeup for **Brick By Brick - 1** from Override CTF 2026.

## Category
Steganography / Forensics

## Difficulty
Easy

## Description
The challenge provided a PNG image of Sukuna (`challenge.png`). The hint emphasized that there was "no encryption, only layers of obfuscation," suggesting that the flag was hidden through metadata or simple encoding schemes rather than cryptographic algorithms.

## Initial Analysis
Using standard forensic tools, I inspected the image for hidden data:
- `file challenge.png`: Confirmed it was a standard PNG image.
- `binwalk challenge.png`: No hidden files or trailing data detected.
- `exiftool challenge.png`: Revealed an unusual string in the metadata/comment fields.
- `strings challenge.png`: Discovered a suspicious hexadecimal string embedded within the text chunks.

The string found was: `6d346e373133`.

## Tools Used
- `strings` (Binary text extraction)
- `ExifTool` (Metadata analysis)
- `Python` (Hex decoding)

## Approach
1. **Metadata Scavenging:** Search for any non-standard strings in the PNG's metadata or comments.
2. **Encoding Identification:** Recognize the 12-character string `6d346e373133` as hexadecimal.
3. **Layer Decryption:** Decode the hex string to see if it reveals a passphrase or a portion of the flag.
4. **Flag Reconstruction:** Combine the decoded string with the flag format provided in the challenge.

## Steps to Solve
### 1. Locating the Hidden String
A simple `strings` command revealed the obfuscated data:
```bash
strings challenge.png | grep -E "[0-9a-f]{10,}"
```
*Output:* `6d346e373133`

### 2. Decoding the Hex
The string looked like a hex-encoded ASCII value. I used Python to decode it:
```python
>>> bytes.fromhex("6d346e373133").decode()
'm4n713'
```

### 3. Final Result
The decoded string `m4n713` was part of the flag and also served as a crucial piece of information for the second part of the "Brick By Brick" series.

## Flag
`OVRD{enc0d1ng_d0n3_r1gh7_6d346e373133}`

## What I Learned
- **Text Chunks in PNG:** PNG files have various chunks (like `tEXt`) where developers can store metadata that isn't always visible in standard properties viewers.
- **Hex as Obfuscation:** Recognizing hex patterns is a fundamental skill in forensics; any string containing only `0-9` and `a-f` should be tested for hex-to-ASCII conversion.
- **Multi-part Challenges:** Learning to document findings carefully, as strings found in "Easy" challenges often act as passphrases for "Hard" challenges later in the event.

## References
_N/A_
