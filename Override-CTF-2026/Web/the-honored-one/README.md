# The Honored One

## Overview
Writeup for **The Honored One** from Override CTF 2026.

## Category
Web / Reverse Engineering (WASM)

## Difficulty
Medium

## Description
The challenge was a JJK-themed browser game where Gojo Satoru had to defeat Ryomen Sukuna. While the interface was standard HTML/JS, the core game logic and win conditions were managed by a WebAssembly module (`main.wasm`). Sukuna had 1000 HP, and clicking "Attack" manually was a distraction—the real goal was to interact with the WASM exports directly.

## Initial Analysis
Inspecting the network tab and source files revealed:
- `main.wasm`: The binary containing the game state and logic.
- `index.js`: The glue code that initialized the WASM module using Emscripten.

The exported functions from WASM included:
- `gojo_attack()`: Decremented Sukuna's HP and returned a status string.
- `get_sukuna_health()`: Returned current HP.
- `get_gojo_health()`: Returned Gojo's HP.

The `gojo_attack()` function returned a pointer to a C-style string in the WASM linear memory. My analysis suggested that once Sukuna's HP reached 0, this function would return the flag.

## Tools Used
- Browser Developer Tools (Console & Network tab)
- `curl` (To download the WASM binary)
- `Node.js` (For local automated exploitation)
- `WASM2WAT` (For inspecting the underlying instructions)

## Approach
1. **Automation:** Instead of clicking the button 100 times, I would call the exported `gojo_attack()` function in a loop.
2. **Memory Inspection:** Since the function returns a pointer, I needed to read the data at that memory address until the null terminator (`0x00`) was reached.
3. **Local Emulation:** If the browser environment was restricted, I planned to use Node.js to instantiate the module with the necessary imports and extract the flag.

## Steps to Solve
### 1. Browser Console Shortcut
Since the `Module` was globally accessible, I ran a quick loop in the console to automate the attacks:
```javascript
for (let i = 0; i < 101; i++) {
    let result = Module.cwrap('gojo_attack', 'string', [])();
    if (result.includes("OVRD")) {
        console.log("Found Flag:", result);
        break;
    }
}
```

### 2. Local Node.js Solve (Advanced Method)
To solve it locally, I wrote a Node.js script to instantiate the WASM and handle the Emscripten/WASI imports.

```javascript
const fs = require("fs");
const buf = fs.readFileSync("main.wasm");
let memory;

const imports = {
    env: {
        emscripten_resize_heap: () => 0,
        _abort_js: () => { throw new Error("abort"); },
    },
    wasi_snapshot_preview1: {
        fd_write: () => 0,
        fd_close: () => 0,
        fd_seek: () => 0,
    }
};

WebAssembly.instantiate(buf, imports).then(({ instance }) => {
    const exps = instance.exports;
    memory = exps.memory;

    // Helper to read C-string from WASM memory
    const readStr = (ptr) => {
        const mem = new Uint8Array(memory.buffer);
        let end = ptr;
        while (mem[end] !== 0) end++;
        return new TextDecoder().decode(mem.slice(ptr, end));
    };

    // Automate the win condition
    for (let i = 0; i < 110; i++) {
        const ptr = exps.gojo_attack();
        const msg = readStr(ptr);
        if (msg.includes("OVRD")) {
            console.log(`[+] Flag recovered on attack ${i}: ${msg}`);
            break;
        }
    }
});
```

## Flag
`OVRD{G0j0_i5_th3_h0n0r3d_0n3_238d83xd29}`

## What I Learned
- **WASM Exports:** How to interact with C/C++ logic compiled to WebAssembly from the JavaScript context.
- **Linear Memory:** Understanding how WASM stores strings as raw bytes in a shared buffer and how to pointer-chase those strings.
- **Client-Side Secrets:** A reminder that WASM is not a "black box"—it is just as reversible as JavaScript or native binaries, and secrets should never be stored in it.

## References
_N/A_
