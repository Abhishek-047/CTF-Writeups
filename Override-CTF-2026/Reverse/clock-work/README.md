# ClockWork

## Overview
Writeup for **ClockWork** from Override CTF 2026.

## Category
Reverse Engineering

## Difficulty
Easy / Medium

## Description
The challenge provided a ZIP archive containing an ELF binary (`clockwork`) and an encrypted flag file (`flag.enc`). The goal was to recover an 8-character key from the binary, which is used to decrypt the flag. The binary was noted as "not stripped," making it a perfect target for symbol analysis.

## Initial Analysis
Using basic identification tools:
- **File Type:** `ELF 64-bit LSB executable, x86-64`.
- **Symbols:** Running `nm clockwork` revealed several descriptive function names:
  - `main`
  - `check_key`
  - `decrypt_flag`
  - `load_file`
  - `xor_data`

This confirmed that the key validation logic resides in `check_key` and the decryption uses a standard XOR-based routine.

## Tools Used
- `file` / `strings` / `nm` (Initial reconnaissance)
- `Ghidra` (Static analysis and decompilation)
- `GDB` (Dynamic analysis)

## Approach
1. **Static Analysis:** Decompile the `check_key` function to understand the validation logic.
2. **Key Recovery:** Extract the hardcoded characters or logic used to verify the 8-character input.
3. **Execution:** Run the binary with the recovered key to produce the decrypted flag.

## Steps to Solve
### 1. Reversing `check_key`
Opening the binary in **Ghidra**, I navigated to the `check_key` function. The decompiled code performed simple character-by-character comparisons:

```c
if (input[0] == 'c') {
    if (input[1] == 'l') {
        if (input[2] == '0') {
            if (input[3] == 'c') {
                if (input[4] == 'k') {
                    if (input[5] == 'T') {
                        if (input[6] == '0') {
                            if (input[7] == 'k') {
                                return 1; // Key is correct
                            }
                        }
                    }
                }
            }
        }
    }
}
return 0;
```

### 2. Key Identification
By concatenating these characters, the correct 8-character key was identified:
**Key:** `cl0ckT0k`

### 3. Dynamic Verification (Alternative Method)
If static analysis had been obfuscated, I could have used **GDB** to break at the comparison points:
```gdb
gdb ./clockwork
b check_key
run
# Observe register comparisons (e.g., cmp al, 0x63)
```

### 4. Decrypting the Flag
Running the binary and providing the recovered key:
```bash
./clockwork
Enter key: cl0ckT0k
[+] Key Correct! Decrypting flag...
```

## Flag
`OVRD{clockwork_t1ck_t0ck_r3v}`

## What I Learned
- **Importance of Symbols:** Non-stripped binaries significantly reduce reversing time by providing a roadmap of the developer's intent.
- **Static Analysis Basics:** Decompiling nested `if` statements to reconstruct strings.
- **Workflow:** How binaries often separate validation logic (`check_key`) from transformation logic (`decrypt_flag`).

## References
_N/A_
