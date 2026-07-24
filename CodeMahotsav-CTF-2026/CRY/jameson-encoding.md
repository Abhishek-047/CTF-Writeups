## **Jameson Encoding**

### **150**

0  0

J. Jonah Jameson has been raving on the Daily Bugle's internal wire again, ranting in his usual four exclamations. Somewhere in his tirade is a transmission that isn't just noise decode it to find out what he actually said.

---

Someone at the Daily Bugle intercepted one of J. Jonah Jameson's rants mid-transmission. It's mostly the same four words over and over... but not entirely. Figure out what he's really saying.

```plain text
File:jameson_transmission.txt
```

`SHA1: 846d7fccb888180a7aa0bd8d4ad66f751afcf6aa`




## Jameson Encoding

- **CTF:** Event Name

- **Category:** Crypto / Stego

- **Points:** 150

- **Difficulty:** Medium

- **Status:** Solved ✅

### 📝 Description

> J. Jonah Jameson has been raving on the Daily Bugle's internal wire again, ranting in his usual four exclamations. Somewhere in his tirade is a transmission that isn't just noise — decode it to find out what he actually said. It's mostly the same four words over and over... but not entirely. Figure out what he's really saying.

### 🔍 Initial Analysis

The file is a plain-text "transmission" consisting almost entirely of four repeated exclamations: `PARKER!`, `MENACE!`, `SPIDER!`, and `LAWSUIT!`, arranged in blocks of lines. Sitting alone on its own line in the middle of the file was a suspicious, isolated string of base64-looking text:

```plain text
Q00yNntqX2owbjRoX2o0bTNzMG5fdzBuX3RoMXNfdDFtM30=
```

Given the challenge title ("Jameson Encoding") and the hint that the words repeat "but not entirely," there were two competing leads: the embedded base64 blob, and the pattern of the four words themselves.

### 🛠️ Tools Used

- `bash` / `base64 -d`

- Python 3 (`collections.Counter`, `itertools.permutations`)

### 🚀 Approach

1. First tried the obvious path: decode the embedded base64 string directly.

1. Since that flag was rejected, went back to the actual chant text. The challenge explicitly says the repetition "isn't entirely" uniform — meaning the *pattern* of which word appears where is the real signal, not the base64 string.

1. Stripped the four words out of the file (140 words total) and ran a frequency count:

1. With exactly 4 distinct symbols, the natural encoding is **2 bits per word** (4 words = 2² combinations), turning the whole transmission into a binary stream that could be regrouped into bytes → ASCII text.

1. Brute-forced all 4! = 24 possible mappings of `{PARKER, MENACE, SPIDER, LAWSUIT}` → `{00, 01, 10, 11}`, converted each resulting bitstream to bytes, and checked for printable ASCII output.

### 🏁 Solving Steps

1. Extract and clean all 140 words (strip `!`) from `jameson_transmission.txt`.

1. Map each word to a 2-bit symbol and concatenate into one long bitstring (140 words × 2 bits = 280 bits = 35 bytes).

1. Iterate over all 24 permutations of the 4-symbol → 2-bit mapping, decoding each resulting byte stream as ASCII.

1. One permutation produced clean, printable text:

1. Decoding with this mapping revealed the real flag directly in the byte stream — the base64 string was a red herring designed to look like a solved challenge.

### 🚩 Flag

`CM26{p3t3r_p4rk3r_1s_sp1d3rm4n_100}`

### 💡 What I Learned

- Don't stop at the first "flag-shaped" string you find — challenges can plant convincing decoys (a valid-looking base64 blob) to catch solvers who don't finish analyzing the rest of the file.

- When a message repeats over a small fixed alphabet of symbols (here, exactly 4 words), consider that the *symbol choice itself* may be a positional encoding (e.g., 2 bits per symbol for a 4-symbol alphabet) rather than just noise/flavor text.

- Brute-forcing a small permutation space (4! = 24) is cheap and effective when you don't know the symbol-to-bit assignment in advance.

### 🔗 References

- [Base64 encoding — Wikipedia](https://en.wikipedia.org/wiki/Base64)

- General technique reference: n-ary symbol streams as bit-packed encodings (similar to Bacon's cipher / custom base-4 ciphers)
