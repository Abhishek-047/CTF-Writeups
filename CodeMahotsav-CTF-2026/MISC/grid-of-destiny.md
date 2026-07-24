## **Grid of Destiny**

### **250**

1 (100% liked)  0

A 20x20 tactical grid. Chart a course from (0,0) to (19,19) and escape.

MAP LEGEND # Wall impassable . Empty tile safe to stand on O Recharge Station steps on it refill your ring to 100% charge X Black Hole instant death, no exceptions

RULES 1. Movement is orthogonal only: up, down, left, right. No diagonals. 2. Every step you take drains 5% of your ring's charge. 3. Your ring starts at 100% charge. 4. If your charge is too low to survive the next step, you are stranded on the grid forever. 5. Landing on 'O' instantly restores your ring to 100% charge. 6. Landing on 'X' ends the run immediately, regardless of charge.

The shortest way across this grid is also the fastest way to die. Somewhere in the winding of it, there is a way through.

Submit your path as: (0,0), (0,1), (1,1), ...

`nc 143.110.248.48 9002`

write up - 



# Grid of Destiny

- **CTF:** Capture The Flag 2026

- **Category:** Misc

- **Points:** 250

- **Difficulty:** Medium

- **Status:** Solved ✅

## 📝 Description

> A 20×20 tactical grid. Chart a course from (0,0) to (19,19) while managing your ring's battery. Every move costs 5% charge, recharge stations restore the battery to 100%, walls are impassable, and black holes instantly end the run.

## 🔍 Initial Analysis

The challenge provides a 20×20 grid with the following tile types:

- `.` → Empty tile

- `#` → Wall (cannot pass)

- `O` → Recharge station (battery restored to 100%)

- `X` → Black hole (instant death)

Rules:

- Start at `(0,0)`

- Goal at `(19,19)`

- Movement only in four directions

- Battery starts at 100%

- Every move consumes 5% battery

- Recharge stations reset battery to 100%

The important observation is that the battery becomes part of the search state. Since every move costs 5%, a maximum of 20 moves can be taken before requiring a recharge.

## 🛠️ Tools Used

- Python 3

- Breadth-First Search (BFS)

## 🧠 Solution

A normal BFS over coordinates is insufficient because reaching the same position with different remaining battery levels represents different states.

Instead, each node in the search was represented as:

```plain text
(row, column, battery)
```

The BFS algorithm:

1. Start from `(0,0,100)`.

1. Explore four directions.

1. Skip walls (`#`) and black holes (`X`).

1. Reduce battery by 5% for every move.

1. Restore battery to 100% when stepping onto `O`.

1. Store visited states as `(row, col, battery)`.

1. Save parent pointers for path reconstruction.

1. Once `(19,19)` is reached, reconstruct the full path and submit it.

## 🚩 Flag

```plain text
CM26{w1llp0w3r_b34ts_d4rkn3ss_912}
```

## 📚 What I Learned

- Battery or resource constraints should be modeled as part of the search state.

- BFS can solve shortest-path problems with additional state variables.

- Parent tracking is useful for reconstructing paths after graph traversal.

- State-space search is a common technique in CTF Misc and AI-style challenges.

# Last login: Sun Jul 19 17:48:06 on ttys004
abhishek@Abhisheks-MacBook-Pro-3 ~ % cd Desktop
abhishek@Abhisheks-MacBook-Pro-3 Desktop % nano [solve.py](http://solve.py/)
abhishek@Abhisheks-MacBook-Pro-3 Desktop % python3 [solve.py](http://solve.py/)
(0,0),(1,0),(1,1),(1,2),(1,3),(1,4),(1,5),(1,6),(1,7),(1,8),(1,9),(1,10),(1,11),(1,12),(1,13),(1,14),(1,15),(2,15),(3,15),(4,15),(5,15),(6,15),(7,15),(7,14),(7,13),(7,12),(7,11),(7,10),(7,9),(7,8),(7,7),(7,6),(7,5),(7,4),(7,3),(8,3),(9,3),(10,3),(11,3),(12,3),(13,3),(13,4),(13,5),(13,6),(13,7),(13,8),(13,9),(13,10),(13,11),(13,12),(13,13),(13,14),(13,15),(13,16),(14,16),(15,16),(16,16),(17,16),(18,16),(19,16),(19,17),(19,18),(19,19)
abhishek@Abhisheks-MacBook-Pro-3 Desktop % nc 143.110.248.48 9002

```plain text
             THE GRID OF DESTINY
```

==============================================================
Legend: # wall   . empty   O recharge (->100%)   X black hole (death)
Start: (0, 0)    Goal: (19, 19)    Cost: 5% charge per step, no diagonals

```plain text
  0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19
```

0  .  .  .  .  .  X  .  .  .  .  .  X  .  .  .  .  .  X  .  .
1  .  .  .  .  .  .  .  .  .  .  O  .  .  .  .  .  #  #  #  .
2  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  #  .
3  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  #  .
4  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  #  .
5  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  #  .
6  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  #  .
7  .  #  #  .  .  .  .  .  .  .  .  .  .  .  .  O  #  #  #  X
8  X  #  #  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .
9  .  #  #  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .
10  .  #  #  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .
11  .  #  #  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .
12  .  #  #  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .
13  .  #  #  O  .  .  .  .  .  .  .  .  .  .  .  .  O  #  #  X
14  X  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  .
15  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  .
16  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  .
17  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  #  #  .
18  .  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  .  .  .  .
19  .  .  .  .  .  .  X  .  .  .  .  .  X  .  .  .  .  .  .  .

Enter your path, e.g. (0,0), (0,1), (1,1), ...

> (0,0),(1,0),(1,1),(1,2),(1,3),(1,4),(1,5),(1,6),(1,7),(1,8),(1,9),(1,10),(1,11),(1,12),(1,13),(1,14),(1,15),(2,15),(3,15),(4,15),(5,15),(6,15),(7,15),(7,14),(7,13),(7,12),(7,11),(7,10),(7,9),(7,8),(7,7),(7,6),(7,5),(7,4),(7,3),(8,3),(9,3),(10,3),(11,3),(12,3),(13,3),(13,4),(13,5),(13,6),(13,7),(13,8),(13,9),(13,10),(13,11),(13,12),(13,13),(13,14),(13,15),(13,16),(14,16),(15,16),(16,16),(17,16),(18,16),(19,16),(19,17),(19,18),(19,19)

You reach (19, 19) with 55% charge to spare.
FLAG: CM26{w1llp0w3r_b34ts_d4rkn3ss_912}
