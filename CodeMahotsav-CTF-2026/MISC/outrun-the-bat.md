## **Outrun the Bat**

### **250**

1 (100% liked)  0

Something in the dark is waiting for you to blink.

Connect and you'll get 50 rounds, back to back. Each one hands you a fresh nonce and a modulus hash it, take the mod, send the number back. The math is trivial. The clock isn't: you've got 200 milliseconds from the moment the question lands to get your answer in. Answer correctly but a beat too slow, and it'll almost feel like winning.

Fifty rounds. Two hundred milliseconds each. No room to think, and no human hands are fast enough to type it.

`nc 143.110.248.48 9001`

writeup - 


# Outrun the Bat

- **CTF:** Capture The Flag 2026

- **Category:** Misc

- **Points:** 250

- **Difficulty:** Medium

- **Status:** Solved ✅

## 📝 Description

> Survive 50 rounds. Each round provides a nonce and a modulus. Compute the SHA256 hash of the nonce, convert the hash to an integer, take the modulus, and submit the answer within 200 milliseconds. Complete all 50 rounds to obtain the flag.

## 🔍 Initial Analysis

The challenge was not cryptographically difficult—the main obstacle was the strict **200 ms time limit** per round.

Each round required calculating:

```plain text
SHA256(nonce) → Integer → Integer % Modulus
```

Manually solving even a single round was impossible within the given time limit, making automation the intended solution.

## 🛠️ Tools Used

- Python 3

- `socket`

- `hashlib`

- `re`

## 🧠 Solution

A Python script was written to automate the entire interaction with the remote service.

The script performed the following steps:

1. Connected to the TCP server.

1. Read each challenge prompt.

1. Extracted the nonce and modulus using regular expressions.

1. Computed the SHA256 hash of the nonce.

1. Converted the 32-byte hash into a big-endian integer.

1. Calculated the result modulo the provided modulus.

1. Sent the answer immediately.

1. Repeated the process for all 50 rounds.

Using Python's standard library, each computation completed well within the 200 ms limit, allowing all rounds to be solved automatically.

Core calculation:

```python
digest = hashlib.sha256(nonce.encode()).digest()
value = int.from_bytes(digest, "big") % modulus
```

## 🚩 Flag

```plain text
CM26{n0t_3v3n_4_c0mput3r_c4n_outrun_th3_b4t}
```

## 📚 What I Learned

- Network-based CTF challenges often require automation rather than manual interaction.

- Python's `socket` module is sufficient for communicating with TCP services.

- `hashlib` provides efficient implementations of common cryptographic hash functions.

- Binary hash outputs can be converted into integers using `int.from_bytes()`.

- Regular expressions are useful for extracting structured values from server responses.

- Time-limited challenges emphasize scripting speed and efficient parsing over algorithmic complexity.



┌──(parallels㉿kali-linux-2025-2)-[~]
└─$ nano [solve.py](http://solve.py/)

# ┌──(parallels㉿kali-linux-2025-2)-[~]
└─$ python3 [solve.py](http://solve.py/)

# CM26 :: OUTRUN THE BAT

Survive 50 rounds. Each round you get a nonce and a modulus.
Reply with SHA256(nonce) as an integer, mod the given number -- as plain decimal text, one line, within 200ms of receiving the challenge.

[1/50] Calculate: (SHA256(HkDBsvdV) converted to an integer) % 30
Correct in 45.3ms. Next round.
[2/50] Calculate: (SHA256(d93k1CdS) converted to an integer) % 45
Correct in 38.2ms. Next round.
[3/50] Calculate: (SHA256(ArNFy2s8) converted to an integer) % 45
Correct in 37.5ms. Next round.
[4/50] Calculate: (SHA256(FgdcUB63) converted to an integer) % 4
Correct in 38.1ms. Next round.
[5/50] Calculate: (SHA256(YIRTspMM) converted to an integer) % 39
Correct in 37.4ms. Next round.
[6/50] Calculate: (SHA256(YpWXn0IU) converted to an integer) % 13
Correct in 37.6ms. Next round.
[7/50] Calculate: (SHA256(OikGW1ss) converted to an integer) % 28
Correct in 39.0ms. Next round.
[8/50] Calculate: (SHA256(0EpYyH4g) converted to an integer) % 10
Correct in 37.9ms. Next round.
[9/50] Calculate: (SHA256(IGlXb5nb) converted to an integer) % 14
Correct in 38.2ms. Next round.
[10/50] Calculate: (SHA256(SoKq4PfJ) converted to an integer) % 44
Correct in 38.0ms. Next round.
[11/50] Calculate: (SHA256(7O7rJDa3) converted to an integer) % 33
Correct in 42.1ms. Next round.
[12/50] Calculate: (SHA256(tc3QfCTH) converted to an integer) % 25
Correct in 37.6ms. Next round.
[13/50] Calculate: (SHA256(EpK0mCey) converted to an integer) % 11
Correct in 37.8ms. Next round.
[14/50] Calculate: (SHA256(cILL9MUS) converted to an integer) % 19
Correct in 37.0ms. Next round.
[15/50] Calculate: (SHA256(PIjbaZLa) converted to an integer) % 14
Correct in 37.2ms. Next round.
[16/50] Calculate: (SHA256(8MyfHL3L) converted to an integer) % 39
Correct in 36.5ms. Next round.
[17/50] Calculate: (SHA256(L52abCGH) converted to an integer) % 24
Correct in 36.8ms. Next round.
[18/50] Calculate: (SHA256(qu1Pw2ku) converted to an integer) % 49
Correct in 36.8ms. Next round.
[19/50] Calculate: (SHA256(lffbolVm) converted to an integer) % 9
Correct in 37.2ms. Next round.
[20/50] Calculate: (SHA256(JZYg25j2) converted to an integer) % 44
Correct in 36.7ms. Next round.
[21/50] Calculate: (SHA256(4yRzn7j2) converted to an integer) % 10
Correct in 36.4ms. Next round.
[22/50] Calculate: (SHA256(nzyuasx4) converted to an integer) % 34
Correct in 36.6ms. Next round.
[23/50] Calculate: (SHA256(llWSxFRS) converted to an integer) % 36
Correct in 37.3ms. Next round.
[24/50] Calculate: (SHA256(9V9G4pMf) converted to an integer) % 42
Correct in 36.5ms. Next round.
[25/50] Calculate: (SHA256(mE7xVIEG) converted to an integer) % 41
Correct in 37.9ms. Next round.
[26/50] Calculate: (SHA256(4vfI6vbd) converted to an integer) % 3
Correct in 37.8ms. Next round.
[27/50] Calculate: (SHA256(bAhrdRpK) converted to an integer) % 47
Correct in 36.8ms. Next round.
[28/50] Calculate: (SHA256(NnGOLRcf) converted to an integer) % 21
Correct in 36.9ms. Next round.
[29/50] Calculate: (SHA256(kl3b0NMt) converted to an integer) % 6
Correct in 37.7ms. Next round.
[30/50] Calculate: (SHA256(hOAZB3xs) converted to an integer) % 40
Correct in 37.4ms. Next round.
[31/50] Calculate: (SHA256(wkvB3eWm) converted to an integer) % 34
Correct in 37.2ms. Next round.
[32/50] Calculate: (SHA256(JhSLxhAN) converted to an integer) % 9
Correct in 37.7ms. Next round.
[33/50] Calculate: (SHA256(7ttUGP5B) converted to an integer) % 8
Correct in 36.3ms. Next round.
[34/50] Calculate: (SHA256(fObdtVtV) converted to an integer) % 48
Correct in 36.9ms. Next round.
[35/50] Calculate: (SHA256(QfrVsOr4) converted to an integer) % 27
Correct in 37.9ms. Next round.
[36/50] Calculate: (SHA256(o2vY7hbB) converted to an integer) % 33
Correct in 36.6ms. Next round.
[37/50] Calculate: (SHA256(jKvCJEpk) converted to an integer) % 31
Correct in 36.3ms. Next round.
[38/50] Calculate: (SHA256(M1OfqhFs) converted to an integer) % 35
Correct in 36.7ms. Next round.
[39/50] Calculate: (SHA256(fqd6iO44) converted to an integer) % 42
Correct in 37.5ms. Next round.
[40/50] Calculate: (SHA256(Qus0wbdE) converted to an integer) % 27
Correct in 37.1ms. Next round.
[41/50] Calculate: (SHA256(TReuQp7M) converted to an integer) % 49
Correct in 37.7ms. Next round.
[42/50] Calculate: (SHA256(Qw1q05b1) converted to an integer) % 20
Correct in 37.5ms. Next round.
[43/50] Calculate: (SHA256(YWJLTpE1) converted to an integer) % 33
Correct in 38.8ms. Next round.
[44/50] Calculate: (SHA256(3bN8NtcF) converted to an integer) % 28
Correct in 36.9ms. Next round.
[45/50] Calculate: (SHA256(5YjSYSEf) converted to an integer) % 38
Correct in 37.3ms. Next round.
[46/50] Calculate: (SHA256(bObVZS12) converted to an integer) % 7
Correct in 37.3ms. Next round.
[47/50] Calculate: (SHA256(GXr4mgpN) converted to an integer) % 32
Correct in 37.6ms. Next round.
[48/50] Calculate: (SHA256(hwY2KejG) converted to an integer) % 44
Correct in 37.2ms. Next round.
[49/50] Calculate: (SHA256(YnGMY7NA) converted to an integer) % 16
Correct in 36.8ms. Next round.
[50/50] Calculate: (SHA256(mH2BYzRh) converted to an integer) % 14
Correct in 37.4ms. Next round.
All 50 rounds survived.
FLAG: CM26{n0t_3v3n_4_c0mput3r_c4n_outrun_th3_b4t}

┌──(parallels㉿kali-linux-2025-2)-[~]
└─$
