# Parallax Frame

## Overview
Writeup for **Parallax Frame** from Override CTF 2026.

## Category
OSINT / GeoGuessr

## Difficulty
Medium

## Description
The challenge provided a single image of a modern glass building with the prompt: *“A mechanism of glass and symmetry. Nothing is random — every detail is part of the system. If you can read the structure, you can find where it stands. Precision is key.”* The goal was to find the exact coordinates ($\pm 10$ meters).

## Initial Analysis
The image featured a modern, blue-glass office tower with symmetrical wings and corporate landscaping. The clean, high-tech aesthetic strongly suggested a major Indian IT hub, likely **Bengaluru** or **Hyderabad**. 

The title "Parallax Frame" suggested that perspective was critical, and the challenge required high-precision geolocation rather than just identifying the building.

## Tools Used
- **Google Maps / Street View:** For visual matching and ground truth verification.
- **Google Earth Pro:** For precise coordinate extraction.
- **Browser Developer Tools:** For automating coordinate submission and parsing distance feedback.
- **Triangulation Logic:** Using the server's distance-from-target feedback to narrow down the search area.

## Approach
1. **Architectural Fingerprinting:** Search for IT parks in Bengaluru with blue-glass, symmetrical facades.
2. **Site Identification:** Confirm the building as part of **Bagmane Solarium City, Bengaluru**.
3. **Distance-Based Triangulation:** Since the challenge provided the distance to the target for every wrong guess, I used those values to narrow the search radius.
4. **Coordinate Refinement:** Perform a micro-grid search using decimal coordinate shifts until the threshold was met.

## Steps to Solve
### 1. Identifying the Location
By analyzing the building's specific glass paneling and road layout, I identified the structure within **Bagmane Solarium City** in Bengaluru. 

### 2. Triangulation via Feedback
The server provided feedback like: `Not close enough. Distance: 115.9 m`. 
By submitting a sequence of coordinates in different directions, I observed the distance changes:
- `12.9715316, 77.7107317` $\rightarrow$ 115.9 m
- `12.9721200, 77.7107800` $\rightarrow$ 85.1 m (Getting closer)
- `12.9718212, 77.7108853` $\rightarrow$ 83.9 m

### 3. Grid Search Automation
To find the exact "pin-drop" location, I used a simple JavaScript loop in the browser console to test a grid of coordinates around the best-performing point.

```javascript
async function findExactCoords(baseLat, baseLon) {
    let step = 0.00005; // ~5 meters
    for (let i = -5; i <= 5; i++) {
        for (let j = -5; j <= 5; j++) {
            let lat = baseLat + (i * step);
            let lon = baseLon + (j * step);
            
            // Interaction logic with challenge UI
            document.querySelectorAll("input")[0].value = lat;
            document.querySelectorAll("input")[1].value = lon;
            document.querySelector("button").click();
            
            await new Promise(r => setTimeout(r, 600)); // Delay for server response
            console.log(`Testing: ${lat}, ${lon}`);
        }
    }
}
```

### 4. Final Lock-on
The iteration led to the final coordinates that satisfied the $\pm 10$m requirement.

**Final Coordinates:** `12.97219, 77.71156`

## Flag
`OVRD{p4r4ll4x_fr4m3_g30l0c4t3d}`

## What I Learned
- **Iterative Refinement:** How to use server feedback as a "Hot or Cold" game to perform precise geolocation without a visual match.
- **Architectural Patterns:** Recognizing the "Tech Park" style common in Indian metros.
- **Coordinate Precision:** Understanding the physical distance represented by decimal shifts in latitude and longitude ($0.00001 \approx 1.1$ meters).

## References
_N/A_
