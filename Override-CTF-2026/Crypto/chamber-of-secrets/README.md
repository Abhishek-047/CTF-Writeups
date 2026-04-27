# Chamber of Secrets

## Overview
Writeup for **Chamber of Secrets** from Override CTF 2026.

## Category
Cryptography

## Difficulty
Medium

## Description
The challenge presented a Harry Potter-themed service where users could encrypt messages, decrypt ciphertexts, and retrieve an encrypted "prophecy" (the flag). The goal was to decrypt the flag by identifying a critical flaw in how the system handled its secrets.

## Initial Analysis
Upon inspecting the provided source code (logic), the fatal flaw was in the key derivation process:
```python
iv = os.urandom(16)
key = iv[::-1] + iv
```
The **AES-256 key** was derived directly from the **IV**. If we could recover the IV, the key would be immediately compromised. This pointed toward a **Chosen Ciphertext Attack** against the AES-CBC implementation.

## Tools Used
- `Python 3`
- `PyCryptodome`
- `pwntools`

## Approach
The vulnerability lies in the mathematical property of **AES-CBC decryption**:
- $P_1 = D(C_1) \oplus IV$
- $P_3 = D(C_3) \oplus C_2$

If we supply a crafted ciphertext where $C_3 = C_1$ and $C_2 = 0$ (null bytes), then:
1. $P_1 = D(C_1) \oplus IV$
2. $P_3 = D(C_1) \oplus 0 = D(C_1)$

By XORing $P_1$ and $P_3$, we can isolate the IV:
$$P_1 \oplus P_3 = (D(C_1) \oplus IV) \oplus D(C_1) = IV$$

## Steps to Solve
```python
from pwn import *
from Crypto.Cipher import AES
import re

HOST = "34.93.2.73"
PORT = 8700

r = remote(HOST, PORT)

def recv_menu():
    r.recvuntil(b'Harry : ')

def decrypt_payload(payload):
    recv_menu()
    r.sendline(b'2')
    r.recvuntil(b'> ')
    r.sendline(payload.hex().encode())
    # Server returns the full hex result
    data = r.recvuntil(b'Harry : ')
    return data

# Prophecy/Flag Ciphertext provided by service
flag_ct = bytes.fromhex("672c9f30d69802d849676e2aec5b92cee5f2c97e2b1f50ddea3b7cc7a94b369a6cfa79ab133d3203fe5855f3306034d4")

# Extract first block for the CBC trick
C1 = flag_ct[:16]

# Craft: C1 || NULL_BLOCK || C1
payload = C1 + b"\x00"*16 + C1

# Get decrypted response
resp = decrypt_payload(payload)

# Extract plaintext blocks from the response
match = re.search(rb"([0-9a-fA-F]{96})", resp)
if not match:
    print("[-] Failed to find hex data in response")
    exit()

pt = bytes.fromhex(match.group(1).decode())
P1 = pt[:16]
P3 = pt[32:48]

# Recover IV via mathematical property of CBC
iv = bytes(a ^ b for a, b in zip(P1, P3))

# Derive Key using challenge logic
key = iv[::-1] + iv

# Decrypt the original prophecy
cipher = AES.new(key, AES.MODE_CBC, iv=iv)
flag = cipher.decrypt(flag_ct)

print(f"[+] Recovered IV:  {iv.hex()}")
print(f"[+] Derived Key:   {key.hex()}")
print(f"[+] Decrypted Flag: {flag.decode().strip()}")
```

## Flag
`OVRD{kn0wn_c1p73rt3x7_4tt4ck_1s_fun_r1gh7_ghui3}`

## What I Learned
- **Implementation Risks:** Secure algorithms like AES are only as strong as their implementation. Reusing IV components as keys is a critical failure.
- **Oracle Vulnerabilities:** Providing raw decryption endpoints (decryption oracles) without proper validation can lead to full key recovery in modes like CBC.
- **CBC Mathematics:** Deepened understanding of block chaining and how XORing adjacent blocks can be used to bypass encryption logic.

## References
_N/A_
