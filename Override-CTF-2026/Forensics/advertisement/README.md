# Advertisement

## Overview
Writeup for **Advertisement** from Override CTF 2026.

## Category
Forensics

## Difficulty
Medium

## Description
The challenge provided a forensic disk image named `advertisements.E01`. The goal was to uncover hidden data within a series of advertisement videos stored on an NTFS volume.

## Initial Analysis
Upon inspecting the `advertisements.E01` (Expert Witness Format) file, I discovered it contained an **NTFS filesystem**. Within this volume were several `.mp4` files. 

The breakthrough came when I realized the filesystem utilized **NTFS Alternate Data Streams (ADS)**. This feature allows data to be "hidden" as additional streams attached to a file, which are typically invisible to standard file browsers.

## Tools Used
- `SleuthKit` (`fls`, `icat`)
- `stdbuf` (for unbuffered E01 processing)
- `Python 3` (XOR Brute-forcing & Decoding)
- `Strings` / `ExifTool`

## Approach
1. **Enumerate Filesystem:** List all files and hidden ADS streams recursively.
2. **Identify Patterns:** Notice that each ADS stream was exactly **986 bytes** and appeared randomized, suggesting simple encryption or encoding.
3. **Determine Encoding:** Brute-force single-byte XOR keys looking for common file headers (PNG, XML).
4. **Data Reconstruction:** Extract, decode, and analyze the contents of all streams, focusing on metadata fields (XMP).

## Steps to Solve
```python
import os
import subprocess

IMAGE = "advertisements.E01"

def extract_stream(ewf_file, inode_attr, outfile):
    with open(outfile, "wb") as f:
        subprocess.run(
            ["stdbuf", "-o0", "icat", "-i", "ewf", ewf_file, inode_attr],
            stdout=f,
            stderr=subprocess.DEVNULL
        )

# 1. List all files and ADS streams
result = subprocess.run(
    ["stdbuf", "-o0", "fls", "-r", "-i", "ewf", IMAGE],
    capture_output=True,
    text=True
)

os.makedirs("streams", exist_ok=True)

# 2. Extract ADS streams attached to MP4 files
for line in result.stdout.splitlines():
    if ":" in line and "mp4:" in line.lower():
        parts = line.split()
        inode = parts[1].rstrip(":")
        # Sanitize name for filesystem
        name = parts[-1].replace("/", "_").replace(":", "_")
        extract_stream(IMAGE, inode, f"streams/{name}.bin")

# 3. Decode all streams using XOR key 0x14
print("--- Decoded Content Analysis ---")
for fname in os.listdir("streams"):
    path = os.path.join("streams", fname)
    with open(path, "rb") as f:
        data = f.read()
    
    decoded = bytes(b ^ 0x14 for b in data)
    
    # Filter printable text
    text = "".join(chr(c) if 32 <= c < 127 else "." for c in decoded)
    
    if "OVRD" in text or "rdf" in text or "dc:" in text:
        print(f"\n[+] Found data in: {fname}")
        print(text[:500])
```

## Flag
`OVRD{4ds_4r3_gr347_s0urc3_t0_h1d3_d474_1ft78jn9}`

## What I Learned
- **NTFS ADS Forensics:** How to use SleuthKit to interact with hidden data streams.
- **Encoding Detection:** Recognizing fixed-width random data as a potential XOR target.
- **XMP Forensics:** The importance of checking XML/XMP metadata fields which are often ignored by standard viewers.

## References
_N/A_
