# Nothing

## Overview
Writeup for **Nothing** from Override CTF 2026.

## Category
OSINT / Git Forensics

## Difficulty
Easy

## Description
> You can't find flag from Nothing, can you?

The challenge provided a link to a GitHub repository: `https://github.com/Z3R0-8YT3/Nothing`. At first glance, the repository appeared completely empty, containing only a single `README.md` file with the word "Nothing".

## Initial Analysis
The repository structure was minimal:
- `README.md` (Content: `Nothing`)
- No other visible branches or files.

The challenge description was a direct hint: the flag wasn't in "Nothing"—it was elsewhere.

## Tools Used
- `git` (log, show, fsck, reflog)
- `grep`
- Browser (GitHub OSINT)

## Approach
1. **Internal Forensics:** Exhaustively check the repository's Git internals for hidden blobs, dangling commits, or history manipulations.
2. **Contextual Shift:** Recognizing that the repo truly contained "Nothing," I pivoted to **Open Source Intelligence (OSINT)** on the author's GitHub profile to find related data.
3. **Cross-Repository Search:** Identify other public repositories owned by the same user that might host the flag.

## Steps to Solve
### 1. Cloned and Inspected History
```bash
git clone https://github.com/Z3R0-8YT3/Nothing.git
cd Nothing
git log --oneline --all
```
*Output:*
```text
f7dbfc9 nothing
3bdaa49 Initial commit
```
Inspecting the initial commit `3bdaa49` revealed only a minor change in quotes and spacing—a clear distraction.

### 2. Deep Git Inspection
I ran several commands to ensure no hidden objects existed:
```bash
git fsck --full --unreachable
git reflog
git cat-file -p <commit_hashes>
```
The results confirmed that the flag was absolutely not within the `.git` directory of this repository.

### 3. GitHub OSINT
I visited the author's profile: [https://github.com/Z3R0-8YT3](https://github.com/Z3R0-8YT3).
I discovered another repository: **`UofTCTF-Writeups`**.

### 4. Flag Retrieval
I cloned the second repository and searched for the "OVRD" prefix:
```bash
git clone https://github.com/Z3R0-8YT3/UofTCTF-Writeups.git
cd UofTCTF-Writeups
grep -R "OVRD" .
```
The flag was found hidden within the documentation of this unrelated writeup repository.

## Flag
`OVRD{y0u_4r3_4_g1t_f0r3ns1cs_3xp3r7_j9y65ok}`

## What I Learned
- **Misdirection:** Don't get tunnel-visioned on the provided files if the challenge name or description hints at a broader scope.
- **Git Forensics vs. OSINT:** While Git tools are powerful for internal analysis, OSINT is often the key to solving challenges involving public platforms like GitHub.
- **Author Profiling:** In "person-based" CTF challenges, the author's digital footprint is a valid part of the attack surface.

## References
_N/A_
