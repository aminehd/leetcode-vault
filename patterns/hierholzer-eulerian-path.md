---
tags: [algorithm, graph, eulerian-path, hierholzer, dfs, pinterest]
problems: [reconstruct-itinerary, valid-arrangement-of-pairs]
---

# Hierholzer's Algorithm — Eulerian Path / Circuit

## When to reach for it
The trigger phrase is **"use every EDGE exactly once."** Not nodes — edges.
- Reconstruct a path/itinerary that consumes every ticket/pair/edge once.
- "Valid arrangement of pairs", "reconstruct itinerary", any *traverse-all-edges* problem.
- A node can be visited multiple times; an **edge** is used once. That's the tell that separates this from normal DFS/BFS.

> Plain BFS/DFS visits *nodes* once. Hierholzer consumes *edges* once. Different problem.

## The algorithm (this is ALL of it)
**DFS + append-when-exhausted + reverse.**

```python
def hierholzer(start, graph):
    # graph: node -> iterable of outgoing neighbors (mutated as edges are consumed)
    res = []
    def dfs(node):
        while graph[node]:                 # while this node still has edges
            dfs(pop_an_edge(graph[node]))  # consume one edge, go deeper
        res.append(node)                   # node is EXHAUSTED -> append on the way back
    dfs(start)
    return res[::-1]                        # reverse: post-order -> path order
```

Two non-negotiable details:
1. `res.append(node)` is **after** the while loop (post-order) — you record a node when it has *no edges left*, not when you arrive.
2. You append **`node`** (the airport this call owns), **never the child you just visited**. ← classic bug.
3. **Reverse** at the end.

## Plug-and-play template (adjacency as a param + optional min-heap for ordering)

```python
from collections import defaultdict
import heapq
from typing import List

def eulerian_path(edges: List[List[str]], start: str, lexical: bool = True) -> List[str]:
    # 1. Build adjacency. Min-heap per node => always consume smallest edge first
    #    (only needed when the problem wants the lexicographically smallest path).
    graph = defaultdict(list)
    for u, v in edges:
        if lexical:
            heapq.heappush(graph[u], v)   # heap = "smallest first"
        else:
            graph[u].append(v)            # any order = some valid Eulerian path

    res = []
    def dfs(node):
        adj = graph[node]
        while adj:
            nxt = heapq.heappop(adj) if lexical else adj.pop()
            dfs(nxt)
        res.append(node)                  # APPEND node, not nxt
    dfs(start)
    return res[::-1]
```

- **Hierholzer core** = DFS + append-on-exhaustion + reverse. *No heap involved.*
- **min-heap** = a bolt-on, used ONLY when the problem demands lexical order
  (e.g. Reconstruct Itinerary). Drop it and the core still produces a valid path.

> Mental model:
> **Hierholzer = DFS, append when stuck, reverse.**
> **+ min-heap = "...and make it lexically smallest."**
> Keep them separate.

## Why it's correct (degree-counting)
- A valid Eulerian path exists ⇒ every node has `in == out`, except start (`out = in+1`) and end (`in = out+1`).
- You can only get **permanently stuck** at a node where `in > out` — i.e. the **unique end node**. So the first node appended is provably the path's endpoint.
- After finishing that first start→end trail, the **remaining unused subgraph is fully degree-balanced**. Any walk from a node `u` with leftover edges must return to `u` → it forms a **closed cycle**.
- Post-order append + reverse splices each such cycle in at its anchor `u`, exactly where a detour belongs. Induction on remaining edges ⇒ full Eulerian path. ∎

## Why min-heap gives the LEXICALLY SMALLEST path
- Heap ⇒ "smallest edge first." Hierholzer ⇒ "still a valid (uses-all) path."
- The only case where smallest-first would break validity is a small edge that dead-ends early — but the dead-end is *exactly* the element that must live at the **tail**, and reverse-post-order puts it there automatically. So the two goals never actually fight: you always take the smallest edge you can while still completing the tour.

## Complexity
- `O(E log E)` with the min-heap (each edge pushed/popped once).
- `O(E)` without ordering (plain list/stack).

## Gotchas to remember
- Append `node`, not the child. (The bug that bit me.)
- Append is **after** the loop, not inside it.
- Don't forget to **reverse** the result.
- Start node is given (e.g. `"JFK"`), or is the unique node with `out = in + 1`.

## Linked problems
- LC 332 — [[reconstruct-itinerary]] (Pinterest tagged) — min-heap variant for lexical order.
- LC 2097 — Valid Arrangement of Pairs — same template, `lexical=False`, start = node with `out-in==1`.

Related: [[min-heap]] · graph traversal
