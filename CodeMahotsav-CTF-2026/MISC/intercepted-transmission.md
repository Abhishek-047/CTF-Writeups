**CH1-MISC-01 - Intercepted Transmission
150**Created by Sujhal Gurav 2 (100% liked)  0
A fragment of attacker communication was intercepted mid-transmission and dropped somewhere public sloppy tradecraft, or deliberate noise. Either way, it's the first hard evidence that this wasn't a one-off phishing attempt. Find the intercepted transmission and recover the operator's command.`Source: Pn6uvUCS`

# Intercepted Transmission

- **CTF:** CM26

- **Category:** Misc

- **Points:** 150

- **Difficulty:** Medium

- **Status:** Solved ✅

## 📝 Description

> A fragment of attacker communication was intercepted mid-transmission and dropped somewhere public — sloppy tradecraft, or deliberate noise. Either way, it's the first hard evidence that this wasn't a one-off phishing attempt. Find the intercepted transmission and recover the operator's command.

## 🔍 Initial Analysis

The source pointed to a password-locked Pastebin (`pastebin.com/Pn6uvUCS`). After unlocking it, the paste contained three lines:

```plain text
1. 34 93 76 43 73 15 52 84 36 b3 33 95 d4 54 64 f6 92 82 a2 64 66 16 86 55 84 f6 c3 45 65 63

3. The earliest filmed recording of the Moonwalk was performed by tap dancer Bill Bailey in 1943.
```

- Line 1: a space-separated string of 30 two-character hex tokens (valid hex — some tokens contain `a–f`, confirming base-16, not decimal).

- Line 2: blank (likely just a separator).

- Line 3: a "trivia" statement, presumably included as a hint — or as noise.

First move was to fact-check line 3, since the challenge description explicitly flagged "sloppy tradecraft, or deliberate noise." A quick search showed the claim is inaccurate: Wikipedia credits Bill Bailey's *filmed* moonwalk performance to **1955** (Apollo Theater), not 1943. This confirmed the trivia line was a deliberate red herring rather than a literal key — a nice thematic touch (the challenge is *about* misdirection).

## 🛠️ Tools Used

- Python 3 (custom scripts for XOR/statistical analysis)

- CyberChef (`From Hex`, `Magic` operation)

- Web search (fact-checking the trivia line)

## 🚀 Approach

The core question was: what transform turns 30 hex bytes into readable text?

Theories tested, roughly in order:

1. **Straight hex → ASCII.** Failed — only ~17/30 bytes fell in the printable range (0x20–0x7E); the rest were >0x80, so this couldn't be the whole story.

1. **Single-byte XOR (brute-force all 256 keys).** Failed — byte values spanned 0x15–0xF6 (a 225-value range), wider than the 95-value printable ASCII range, so *no* single-byte XOR/Caesar shift could make all 30 bytes printable. Ruled out mathematically, not just by trial.

1. **Repeating-key XOR / Vigenère** with candidate keys derived from the trivia line (`1943`, `1955`, `BillBailey`, `moonwalk`, `CabCalloway`, etc.), plus chi-squared letter-frequency cryptanalysis to search for an unknown key. Failed.

1. **RC4** with the same candidate keys. Failed.

1. **Nibble-swap** (swap the high/low hex digit of each byte) — this was the breakthrough. It produced a **100% printable** ASCII string. To validate this wasn't coincidental, I ran an empirical simulation: nibble-swapping 200,000 random 30-byte sequences produced **zero** fully-printable results. This confirmed the data was deliberately constructed as ASCII text with swapped nibbles before being hex-encoded.

1. With the nibble-swapped string in hand (`C9g47Q%Hc;3YMEFo)(*FfahUHo<TV6`), tried a second exhaustive layer: all 256 single-byte XOR keys, ~50 thematic wordlist keys, rail-fence transposition, columnar transposition, ROT47/ROT13/Atbash, and letter-only/digit-only extraction. None produced readable text — meaning this string needed a *different class* of tool to finish decoding.

1. Handed the confirmed nibble-swappe string to **CyberChef's Magic** operation, which brute-forces a much larger recipe space (Base-variants, substitution ciphers, etc.) than is practical to hand-roll. This surfaced the final plaintext.

## 🏁 Solving Steps

1. Unlock the Pastebin (`Pn6uvUCS`) to retrieve the three lines of data.

1. Fact-check the trivia line — confirm it's intentionally incorrect (real date: 1955, not 1943), marking it as misdirection rather than a cipher key.

1. Take the 30 hex byte tokens from line 1 and rule out plain hex-to-ASCII and single-byte XOR (byte range too wide to fit printable ASCII under any single shift).

1. Apply a **nibble swap** to every byte (swap each byte's high and low hex digit) → yields a fully printable ASCII string:

1. Validate the nibble-swap wasn't a fluke via Monte Carlo simulation (0/200,000 random trials produce full printability) — confirms deliberate construction.

1. Feed the nibble-swapped string into **CyberChef → Magic** to brute-force the remaining encoding layer.

1. Magic resolves the final layer, recovering the operator's command.

## 🚩 Flag

`CM26{GhostLotus_STAGE-2}`

## 💡 What I Learned

- **Range-based cryptanalysis saves time.** Before brute-forcing keys, checking the *span* of byte values (max − min) against the printable ASCII window (95 values) immediately rules out entire cipher classes — no single-byte XOR/Caesar shift can work if the data's range exceeds ~95.

- **Statistical validation beats intuition.** Rather than assuming a transform "looks right," running a Monte Carlo simulation (e.g., "how often does nibble-swapping random bytes produce full printability?") turns a hunch into near-certainty. 0/200,000 is a strong signal a transform is intentional.

- **Don't skip fact-checking flavor text.** Trivia/lore lines in challenge descriptions aren't always keys — sometimes they're intentionally wrong, and the "wrongness" itself is the clue that it's noise, not signal.

- **Know when to switch tools.** Hand-rolled Python is great for ruling out cipher classes systematically, but for the "kitchen sink" brute-force stage (dozens of obscure encodings), CyberChef's Magic operation is far more efficient than manually enumerating every possibility.

## 🔗 References

- [CyberChef](https://gchq.github.io/CyberChef/)

- [Moonwalk (dance) — Wikipedia](https://en.wikipedia.org/wiki/Moonwalk_(dance)) — used to fact-check the trivia line
