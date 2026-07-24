

## **REACTOR-LOCK VM**

### **150**

0  0

The old reactor bay was sealed off after the incident nobody's touched the access console in years. Maintenance left behind a Python utility that's supposed to unlock it, but nobody remembers the key. Whoever built this clearly didn't trust ordinary passwords: the checker doesn't compare your input directly, it runs it through some kind of custom virtual machine first. Figure out how the lock thinks, and it'll open.

File: vm_challenge.py

## 

REACTOR-LOCK VM

- **CTF:** Event Name

- **Category:** Reverse Engineering

- **Points:** 150

- **Difficulty:** Medium

- **Status:** Solved ✅

### 📝 Description

> The old reactor bay was sealed off after the incident — nobody's touched the access console in years. Maintenance left behind a Python utility that's supposed to unlock it, but nobody remembers the key. Whoever built this clearly didn't trust ordinary passwords: the checker doesn't compare your input directly, it runs it through some kind of custom virtual machine first. Figure out how the lock thinks, and it'll open.

### 🔍 Initial Analysis

The challenge shipped as a single Python file, `vm_challenge.py`. It defines a tiny custom VM with three 8-bit registers (`R1`, `R2`, `R3`) and a 5-instruction ISA: `LOAD`, `XOR_REG`, `ROL` (rotate-left-3), `XOR_MEM`, and `HALT`.

A fixed bytecode program is re-executed once per character of the candidate input string. Before each run, `memory[0..2]` is refreshed with:

- `memory[0]` = the current input character

- `memory[1]` = `KEY_STREAM_A[i]`

- `memory[2]` = `KEY_STREAM_B[i]`

Critically, the **registers are never reset between characters** — only `memory` is refreshed. That means each output byte depends on the accumulated register state from all previous characters, not just the current one. The final list of output bytes (one per input char) is compared against a hardcoded 33-byte array, `ENCRYPTED_CHECK`.

There was also a decoy: a base64 string named `_LEGACY_UNLOCK_STRING`, explicitly commented as unused/dead code, sitting right next to the real check logic — a plausible distractor for anyone grepping for base64 blobs instead of reading the VM.

### 🛠️ Tools Used

- Python 3 (for both simulating and reversing the VM)

- Manual bytecode tracing / algebra (XOR and bit-rotation are trivially invertible)

### 🚀 Approach

Rather than brute-forcing 33 unknown bytes, I traced the fixed bytecode program symbolically for one iteration:

```plain text
R1 ^= mem[0]      # mix in input byteR1 ^= mem[1]      # mix in KEY_STREAM_A[i]R1  = rotl3(R1)R2  = 0x3DR1 ^= R2R3 ^= mem[2]      # mix in KEY_STREAM_B[i] (R3 accumulates across chars!)R1 ^= R3R1  = rotl3(R1)HALT              # R1 is this iteration's output byte
```

Every operation used (`XOR`, fixed `LOAD`, 3-bit rotate) is bijective/invertible:

- `XOR` inverts by re-XORing with the same value.

- `rotl3` inverts with `rotr3`.

So instead of forward-brute-forcing, I ran the whole thing **backwards**, from the ciphertext to the plaintext, carrying the register state (`R1`, `R3`) across iterations exactly as the original VM does — just walking each step's arithmetic in reverse.

### 🏁 Solving Steps

1. Extracted the bytecode logic and rewrote it as a symbolic sequence of operations per character.

1. Noted that `R1` is *not* reset between characters — after each full run, `R1` equals that character's output byte, so `R1` going into iteration `i+1` is `ENCRYPTED_CHECK[i]`.

1. Noted `R3` accumulates: `R3 ^= KEY_STREAM_B[i]` every iteration without reset, so it had to be reconstructed cumulatively while stepping through.

1. Wrote an inversion script that, for each ciphertext byte:

1. Converted the recovered byte sequence to ASCII characters.

1. Verified the result against the original `check_flag()` function from the challenge file directly — confirmed it returns `True`.

1. Identified `_LEGACY_UNLOCK_STRING` as a decoy: it base64-decodes to a different, unrelated string and is explicitly never wired into the actual `check_flag()` logic.

### 🚩 Flag

`CM26{v1br4n1um_sh13ld_v_n4n0t3ch}`

### 💡 What I Learned

- Stateful stream-cipher-like VMs (where registers persist across "rounds") look intimidating but are usually fully invertible one round at a time, as long as every instruction used is bijective (XOR, fixed rotations, table lookups with known keys).

- Always separate *keystream material* (public/known, like `KEY_STREAM_A/B` here) from *unknowns* (the plaintext) — once isolated, XOR-based ciphers reduce to simple algebra.

- Watch for red herrings in source-provided challenges — an unused, suspiciously-labeled base64 constant sitting near the real logic is a classic way to bait a wrong answer.

- Reversing byte-by-byte from the *last* known output backward is often easier than trying to brute force or forward-simulate an unknown input, especially with chained/stateful transforms.

### 🔗 References

- Custom VM/bytecode challenges: general technique — trace ISA semantics, identify invertible ops, invert step-by-step.

- XOR cipher inversion basics: `a ^ b ^ b == a`.
