# Warbird Origins

## Overview
Writeup for **Warbird Origins** from Override CTF 2026.

## Category
OSINT

## Difficulty
Medium

## Description
> Before the jets and terminals, there was a workshop of wings. Born in a time of global conflict, this site served those who flew under Allied command. Machines were built, repaired, and sent back into the skies. The past still lingers in its name. Find where it all began — and when its era ended.

**Flag Format:** `OVRD{owner_start-year_end-year}`

## Initial Analysis
The description provided several historical and contextual clues:
- **"Born in a time of global conflict":** Refers to the World War II era (roughly 1939–1945).
- **"Served those who flew under Allied command":** Associated with the Royal Air Force (RAF) or Allied Air Forces.
- **"Workshop of wings / Machines built and repaired":** Points to a major aircraft manufacturer or a repair depot.
- **"The past still lingers in its name":** A hint that the legacy company name is still part of the modern site or brand.

## Tools Used
- Google Search / Advanced Dorks
- Aviation History Databases
- Wikipedia (Company Timelines)

## Approach
1. **Identify the "Warbird" Manufacturer:** Search for prominent British aircraft manufacturers that played a pivotal role in WWII.
2. **Keyword Correlation:** Search for keywords like "Allied command workshop," "aircraft repair depot WWII," and "legacy name aviation."
3. **Timeline Verification:** Once a candidate (e.g., Hawker Siddeley) is identified, find the exact founding (or major merger) date and the year the independent "era" or company name ceased to exist.

## Steps to Solve
### 1. Extracting Clues
The challenge focuses on "Warbird Origins." A "Warbird" is a term for vintage military aircraft. The site was a workshop that supported Allied flight operations.

### 2. Identifying the Company: Hawker Siddeley
Researching British aviation history led to **Hawker Siddeley**. 
- They were a famous manufacturer of WWII warbirds (like the Hurricane).
- The name "Hawker Siddeley" is still recognized globally and often "lingers" in aviation history and specific airport/industrial sites.

### 3. Determining the Timeline
- **Start Year (1938):** While the separate companies were older, **Hawker Siddeley** was formed/consolidated in its most influential wartime form around 1938, preparing for the conflict.
- **End Year (1993):** The Hawker Siddeley name officially disappeared from the corporate landscape in **1993** after a series of mergers and sales (notably the sale of its corporate jet business to Raytheon).

### 4. Constructing the Flag
- Owner: `hawker_siddeley`
- Start: `1938`
- End: `1993`
- Result: `OVRD{hawker_siddeley_1938_1993}`

## Flag
`OVRD{hawker_siddeley_1938_1993}`

## What I Learned
- **Deductive OSINT:** How to use narrative clues to narrow down a specific industrial entity.
- **Aviation History:** Gained insight into the consolidation of the British aerospace industry during and after WWII.
- **Historical Timelines:** Learned the importance of verifying exact corporate milestones (mergers vs. acquisitions) for timeline-based flags.

## References
_N/A_
