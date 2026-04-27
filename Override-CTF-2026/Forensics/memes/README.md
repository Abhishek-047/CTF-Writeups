# Memes

## Overview
Writeup for **Memes** from Override CTF 2026.

## Category
Forensics / Steganography

## Difficulty
Easy

## Description
The challenge provided a file named `meme.png`, which appeared to be a standard Captain America meme. The description hinted that "every memes are not just memes" and suggested looking for "extra meaning."

## Initial Analysis
At first glance, the image contained a multiplication joke (750 × 1920 = 1440000). While this looked like a distraction, it served as a "trap" to waste time on simple calculations. 

The real anomaly was discovered through deep inspection of the PNG structure. The `IHDR` chunk declared a standard height, but the compressed `IDAT` pixel stream contained **extra hidden pixel rows** that were not rendered by standard image viewers.

## Tools Used
- `ExifTool` / `identify` (Initial metadata check)
- `Python 3` (Custom PNG parser and IDAT decompressor)
- `Pillow` & `NumPy` (Image reconstruction)

## Approach
1. **Metadata Verification:** Rule out EXIF, XMP, or strings-based hiding.
2. **IDAT Analysis:** Extract and decompress the `IDAT` chunks to check for data size mismatches.
3. **Hidden Row Recovery:** Calculate the expected byte count based on the declared height/width and extract the surplus "hidden" rows.
4. **Visual Reconstruction:** Convert the raw surplus bytes back into an image to reveal the flag.

## Steps to Solve
```python
import zlib
import struct
from PIL import Image
import numpy as np

# Path to the challenge file
FILE_PATH = "meme.png"

with open(FILE_PATH, "rb") as f:
    data = f.read()

idat = b""
i = 8  # Skip PNG signature

# Parse chunks
while i < len(data):
    length = struct.unpack(">I", data[i:i+4])[0]
    ctype = data[i+4:i+8]
    cdata = data[i+8:i+8+length]

    if ctype == b"IHDR":
        width = struct.unpack(">I", cdata[0:4])[0]
        height = struct.unpack(">I", cdata[4:8])[0]
        color_type = cdata[9]
        channels = 4 if color_type == 6 else 3
    elif ctype == b"IDAT":
        idat += cdata
    i += 12 + length

# Decompress raw pixel data
raw = zlib.decompress(idat)

# Each row has: 1 filter byte + pixel data
bpr = width * channels + 1
expected_len = height * bpr

# Extract surplus data (Hidden Rows)
extra = raw[expected_len:]
rows = []

for off in range(0, len(extra) - bpr + 1, bpr):
    row = extra[off+1 : off+1 + width*channels]
    if len(row) == width * channels:
        rows.append(np.frombuffer(row, dtype=np.uint8).reshape(width, channels))

if rows:
    arr = np.array(rows)
    # Save the reconstructed hidden area
    img = Image.fromarray(arr[:, :, :3].astype(np.uint8), "RGB")
    img.save("hidden_rows.png")
    print("[+] Successfully saved hidden_rows.png")
else:
    print("[-] No hidden rows found.")
```

## Flag
`OVRD{y0u_s34rch3d_0ut_0f_7h3_b0x_1009m_ry}`

## What I Learned
- **PNG Chunk Manipulation:** Chunks like `IDAT` can store more data than what `IHDR` defines.
- **Manual vs. Automated Tools:** Why specialized tools sometimes fail to detect anomalies inside the compressed stream.
- **Internal Data Alignment:** Understanding how PNG rows (filter byte + pixels) are structured in memory.

## References
_N/A_
