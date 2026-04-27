# JS####

## Overview
Writeup for **JS####** from Override CTF 2026.

## Category
JavaScript / Reverse Engineering

## Difficulty
Medium

## Description
The challenge provided a JavaScript file (`chall.js`) that appeared to be a mess of non-alphanumeric characters. The hint: *"I would have told you but my mom doesn't allow me to swear :("* and the title `JS####` pointed toward a specific type of esoteric obfuscation.

## Initial Analysis
The file content consisted entirely of six characters: `[`, `]`, `(`, `)`, `!`, and `+`. This is the signature of **JSFuck**, an esoteric and educational programming style where any JavaScript code can be written using only these six characters.

**How JSFuck works:**
- `![]` $\rightarrow$ `false`
- `!![]` $\rightarrow$ `true`
- `+[![]+ [][[]]]` $\rightarrow$ `NaN`
- `+[]` $\rightarrow$ `0`
- `+!+[]` $\rightarrow$ `1`

These primitives are used to build strings (e.g., `"constructor"`, `"return"`) and eventually execute arbitrary code using the `Function` constructor.

## Tools Used
- `Node.js` (For local execution)
- `JS-Deobfuscator` / `Browser Console`
- `Manual Analysis` (Identifying the JSFuck pattern)

## Approach
1. **Identification:** Recognize the code as JSFuck based on the character set.
2. **Behavioral Analysis:** Run the script using `node chall.js` to see its output. It printed a decoy message: `"Nothing to see here"`.
3. **Deobfuscation:** Capture the underlying string being evaluated by the JSFuck payload. Since JSFuck scripts eventually call `eval()` or a `Function()` constructor, the goal was to extract the string *before* it was executed.
4. **Flag Extraction:** Analyze the deobfuscated source to find the hardcoded flag.

## Steps to Solve
### 1. Recognizing the Pattern
The code looked like this:
```javascript
[][(![]+ [])[+[]]+ ... + (!![]+ [])[+!+[]]] [([][(![]+ [])[+[]]+ ...])()[ ... ]]
```
This confirms a standard JSFuck encoding.

### 2. Execution & Decoy
Running the script locally:
```bash
node chall.js
# Output: Nothing to see here
```
This indicated that the script was functional but hiding its real payload.

### 3. Deobfuscation Process
To reveal the hidden code, I used the browser's developer console. By removing the final execution parentheses `()` or `()()` from the end of the JSFuck block, the console returns the **string representation** of the code it was about to execute instead of running it.

Alternatively, using an online JSFuck deobfuscator revealed that the payload was constructing a string that contained the flag and then logging a decoy to the console to mislead the user.

### 4. Flag Recovery
The deobfuscated payload revealed the original flag string hidden within the character-building logic.

## Flag
`OVRD{js_i5_a_####ed_up_l4ngu4g3_523d4f}`

## What I Learned
- **Esoteric Languages:** Deepened my understanding of how JavaScript's type coercion (e.g., `![] + []` becoming `"false"`) can be abused for extreme obfuscation.
- **Deobfuscation Techniques:** Learned that for self-executing obfuscated payloads, intercepting the final execution call (like `eval` or `Function`) is the most efficient way to see the "cleartext" code.
- **Misdirection:** Challenges often include a "decoy" output to make the solver believe they haven't found the right path.

## References
_N/A_
