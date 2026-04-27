# StackWhisper

## Overview
Writeup for **StackWhisper** from Override CTF 2026.

## Category
Reverse Engineering

## Difficulty
Hard

## Description
The challenge provided a stripped ELF binary that functioned as a custom Virtual Machine (VM) interpreter. The objective was to reverse the custom bytecode and recover a 10-character "whisper" (password) required to decrypt the hidden flag.

## Initial Analysis
- **File Type:** `ELF 64-bit LSB executable, x86-64, stripped`.
- **Behavior:** The program prompts for a 10-character input. Incorrect inputs result in "Access denied."
- **Internal Structure:** Since the binary is stripped, all function names are removed. Static analysis revealed a large `switch-case` block within a `while` loop, characteristic of a **Bytecode Interpreter**.

## Tools Used
- `Ghidra` (Decompilation and VM structure analysis)
- `GDB` (Dynamic tracing of VM stack and registers)
- `Python` (Reversing the bytecode logic)

## Approach
1. **Instruction Mapping:** Identify the VM's instruction set by tracing the `switch` cases.
2. **Bytecode Extraction:** Locate the embedded bytecode buffer and the input validation routine.
3. **Logic Reversal:** Translate the custom opcodes into readable operations (XOR, ADD, CMP).
4. **Key Recovery:** Solve the character-by-character validation constraints to recover the 10-character password.

## Steps to Solve
### 1. Mapping the VM Opcodes
By analyzing the stack manipulation and register usage in Ghidra, I mapped the custom instruction set:

| Opcode | Mnemonic | Description |
| :--- | :--- | :--- |
| `0x01` | `PUSH` | Push immediate value to stack |
| `0x02` | `LOAD` | Load user input byte to stack |
| `0x03` | `XOR`  | XOR top two stack values |
| `0x04` | `ADD`  | ADD top two stack values |
| `0x05` | `CMP`  | Compare top of stack with expected value |
| `0x06` | `JNZ`  | Jump if not equal |
| `0x07` | `HALT` | Stop execution |

### 2. Reversing the Validation Logic
The bytecode executed a specific sequence for each character of the input. The logic for each character followed a pattern similar to:
$$\text{(input[i] } \oplus \text{ key1) } + \text{ key2 } == \text{ target[i]}$$

### 3. Password Recovery
By extracting the `key` and `target` values from the bytecode for all 10 indices, I reconstructed the valid input:
- **Whisper:** `Sp3cTr4L!z`

### 4. Retrieving the Flag
Running the binary with the recovered whisper:
```bash
./stackwhisper
Enter whisper: Sp3cTr4L!z
Correct! Decrypting...
OVRD{Sp3cTr4L!z}
```

## Flag
`OVRD{Sp3cTr4L!z}`

## What I Learned
- **VM-Based Obfuscation:** How custom interpreters can hide program logic behind an arbitrary instruction set.
- **Stripped Binary Analysis:** Relying on control flow patterns (like the VM loop) rather than symbols to navigate the binary.
- **Dynamic Bytecode Tracing:** Using GDB to watch the VM stack change in real-time, which is often faster than full static decompilation of the interpreter.

## References
_N/A_
