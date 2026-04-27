# just put the flag in the xor bro

## Overview
Writeup for **just put the flag in the xor bro** from Override CTF 2026.

## Category
Crypto

## Difficulty
Easy

## Description
The challenge provided a single hexadecimal ciphertext and a title that practically gave away the encryption method: "just put the flag in the xor bro". The goal was to decrypt the flag using the known prefix format.

**Ciphertext (Hex):**
`3c65313648134b442d13030b40573c0b03012701165a030d2c000740564d19031416`

## Initial Analysis
The title explicitly mentions **XOR**. Given the flag format `OVRD{...}`, this is a textbook case for a **Known-Plaintext Attack (KPA)**. 

If we have:
$$C = P \oplus K$$
Then we can recover the key ($K$) by XORing the ciphertext ($C$) with our known prefix ($P$):
$$K = C \oplus P_{known}$$

## Tools Used
- `Python 3`
- `CyberChef` (Optional verification)

## Approach
1. **Decode Hex:** Convert the hex string into raw bytes.
2. **Key Recovery:** XOR the first 5 bytes of the ciphertext with the known prefix `OVRD{` to discover the starting bytes of the key.
3. **Pattern Identification:** Once the initial key bytes are found, check if the key is a repeating word or phrase.
4. **Full Decryption:** Apply the recovered repeating key to the entire ciphertext to reveal the flag.

## Steps to Solve
```python
# Encrypted hex string from the challenge
cipher_hex = "3c65313648134b442d13030b40573c0b03012701165a030d2c000740564d19031416"
cipher = bytes.fromhex(cipher_hex)

# Recovered repeating key
key = b"s3cr3tx0rk3y"

flag = ""
for i, b in enumerate(cipher):
    # Perform XOR with the corresponding key byte (circular)
    flag += chr(b ^ key[i % len(key)])

print(f"[+] Decrypted Flag: {flag}")
```

## Flag
`OVRD{g3t_x0r3d_y0u_1d10t_3d2e9a3f}`

## What I Learned
- **Known-Plaintext Attack:** The power of knowing the flag format in XOR challenges.
- **XOR Properties:** $A \oplus B = C$ implies $A \oplus C = B$. This property is the foundation of most basic crypto challenges.
- **Repeating Key Detection:** In many CTFs, the XOR key is a readable string rather than random bytes, making it easier to verify once a few characters are recovered.

## References
_N/A_
