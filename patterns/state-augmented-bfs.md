---
tags: [pattern, bfs, shortest-path, grid, state]
problems: [1293, 847, 864, 1091, 994]
---

# State-Augmented BFS — when the node carries more than position

## When to reach for it (recognition signal)
**Shortest path / fewest steps, all moves cost the same (→ BFS), BUT plain `(position)` isn't enough — you also carry a *resource or condition* that changes what you can do next.**
The tell: *"the same cell/node can be revisited in a **better** situation."* Whatever makes it "better" joins the state.

> Core rule: **state = everything that determines the future.** Not how you got there — only what affects what you can still do.

## Base: ordinary BFS (equal-weight shortest path)
- queue of nodes, `visited` set, expand in waves; first time you pop the target = shortest path.
- `visited` is for **termination**, not just speed — and only works if you *read* it (`if x not in visited`), not just write it.

## The twist: augment the state
Add the resource to the node and to `visited`:
```python
q = deque([(start, full_resource, 0)])     # node, resource, dist
visited = {(start, full_resource)}
while q:
    node, res, dist = q.popleft()
    if node == target: return dist
    for nxt in neighbors(node):
        nres = res - cost_to_enter(nxt)     # spend resource if needed
        if nres >= 0 and (nxt, nres) not in visited:
            visited.add((nxt, nres))
            q.append((nxt, nres, dist + 1))
return -1
```
- `(node, resource)` is the visited key — same node, different resource = **different** state.
- `nres >= 0` gates the move; `== 0` (last unit spent) is still legal.
- Two arrivals at the same `(node, resource)`: BFS dequeues the shorter first; the rest are safely dropped (identical futures).

## Family
- **1293 Shortest Path w/ Obstacles Elimination** — resource = walls you may still break. → [[shortest-path-obstacles-elimination]]
- **864 Shortest Path to Get All Keys** — resource = bitmask of keys collected (state = `(r, c, keys)`).
- **847 Shortest Path Visiting All Nodes** — state = `(node, visited-bitmask)`.
- **1091 / 994** — plain grid BFS (2D state) — the *un*-augmented baseline, for contrast.

## Gotchas
- **Pick the minimal sufficient resource.** Walls-left (an int) not the *set of walls broken* (history) — history doesn't affect the future, so it's not in the state. Keeping history blows the state space up needlessly.
- **`visited` on the full augmented key**, or you wrongly block better-resourced arrivals.
- **Resource gate uses `>= 0`** (spending the last unit is allowed).
- **Termination depends on visited** — without it, a free-to-wander region loops forever.

## One-liner to lock it
> Plain BFS keys on *where you are*. State-augmented BFS keys on *where you are + what you've still got*. The resource is part of "where."

See also: [[shortest-path-obstacles-elimination]] · [[backtracking-concept-graph]]
