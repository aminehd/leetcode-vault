---
title: Shortest Path in a Grid with Obstacles Elimination
tags: [pinterest, problem, bfs, grid, state-bfs]
leetcode: 1293
difficulty: hard
technique: "[[state-augmented-bfs]]"
status: done
companies: [pinterest]
---

# 1293. Shortest Path in a Grid with Obstacles Elimination

> Pattern: **state-augmented BFS** — the BFS node carries a *resource budget*. See [[state-augmented-bfs]].

## Problem
Grid of `0` (empty) / `1` (wall). Move 4-directionally from top-left to bottom-right. You may eliminate up to `k` walls. Return the **fewest steps**, or `-1` if impossible.

## Why BFS (not Dijkstra)
Every move costs **1** → all edges equal weight → **BFS** (queue), not Dijkstra (heap). First time you reach the target = shortest path. Reaching for Dijkstra here is over-engineering ("why the heap?").

## The key insight — the state is 3D
A BFS node is **`(row, col, eliminations_left)`**, not `(row, col)`. Arriving at a cell with **more budget left** is a genuinely *stronger* position (can punch through walls ahead), so it's a different state and must be allowed. `visited` stores the **triple**.

## Code
```python
from collections import deque

class Solution:
    def shortestPath(self, grid, k):
        n, m = len(grid), len(grid[0])
        q = deque([(0, 0, k, 0)])          # x, y, remaining, dist
        visited = {(0, 0, k)}
        while q:
            x, y, remaining, dist = q.popleft()
            if x == n - 1 and y == m - 1:
                return dist
            for nx, ny in [(x-1,y),(x,y-1),(x+1,y),(x,y+1)]:
                if not (0 <= nx < n and 0 <= ny < m):
                    continue
                nrem = remaining - grid[nx][ny]        # wall costs 1, empty costs 0
                if nrem >= 0 and (nx, ny, nrem) not in visited:
                    visited.add((nx, ny, nrem))
                    q.append((nx, ny, nrem, dist + 1))
        return -1
```
Optional fast-path: if `k >= n + m - 2`, return `n + m - 2` (enough budget to ignore all walls).

## Gotchas (mine — earned the hard way)
- **State is `(r, c, rem)`, not `(r, c)`.** Same cell with different budget = different state. This is the whole problem.
- **`nrem >= 0`, NOT `> 0`.** Spending your *last* elimination is legal — `nrem == 0` is a valid cell you might win on.
- **`visited` must be *read*, not just written.** I added to it but skipped the `not in visited` check → the set did nothing. `visited` guarantees **termination**: without it, an unreachable target + any open area → infinite wander (`nrem` constant, `dist` →∞, queue never empties). e.g. `[[0,0],[0,1]], k=0` hangs.
- **No value pruning needed** beyond `nrem >= 0`.
- **Start budget** is `k`: `visited = {(0,0,k)}` (not `(0,0,0)`).
- Space around operators: `n - 1 and` not `n -1and` (Py3.12 → `invalid decimal literal`).

## Why visited never blocks a better path (the optimality worry)
**BFS dequeues every state in shortest-distance order**, so the *first* arrival at a triple is its shortest. A "got here sooner with different budget" path is a **different triple** → never blocked. A later arrival at the *same* triple has an identical future → safe to drop.

## Complexity
`O(n·m·k)` states, each touched once → time & space `O(n·m·k)`.

See also: [[state-augmented-bfs]] · [[reconstruct-itinerary]]
