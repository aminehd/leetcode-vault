---
title: Reconstruct Itinerary
tags: [pinterest, problem, graph, eulerian-path, hierholzer, dfs, heap]
leetcode: 332
difficulty: hard
technique: "[[hierholzer-eulerian-path]]"
status: done
companies: [pinterest]
---

# 332. Reconstruct Itinerary

> Technique: [[hierholzer-eulerian-path|Hierholzer's — Eulerian path]]

## Problem
Given a list of airline `tickets` `[from, to]`, reconstruct the itinerary that:
1. starts at `"JFK"`,
2. uses **every ticket exactly once**, and
3. is the **lexicographically smallest** valid itinerary.
A valid itinerary is guaranteed to exist.

## Recognize it
"Use every **edge** (ticket) exactly once" ⇒ **Eulerian path**, not plain BFS/DFS.
Nodes (airports) can repeat; edges (tickets) can't. → [[hierholzer-eulerian-path]].

## Approach
- Adjacency: `airport -> min-heap of destinations` (heap ⇒ always take smallest first = lexical order).
- DFS: while the airport has tickets, pop the smallest and recurse.
- When an airport runs out of tickets, **append it** (post-order).
- **Reverse** the result.

Why it's lexically smallest: heap = "smallest first", Hierholzer = "still valid"; the only edge that would break validity (a small dead-end) is exactly the one that belongs at the tail, which reverse-post-order places there automatically. (Full proof in the technique note.)

## Code
```python
from collections import defaultdict
import heapq
from typing import List

class Solution:
    def findItinerary(self, tickets: List[List[str]]) -> List[str]:
        graph = defaultdict(list)
        for src, dst in tickets:
            heapq.heappush(graph[src], dst)   # min-heap = smallest dest first

        res = []
        def dfs(airport):
            while graph[airport]:
                dfs(heapq.heappop(graph[airport]))
            res.append(airport)               # append the node THIS call owns, after it's drained
        dfs("JFK")
        return res[::-1]                        # reverse post-order -> itinerary order
```

## Complexity
`O(E log E)` — each ticket pushed/popped from a heap once.

## Gotchas (mine)
- Append **`airport`**, not the child you just visited (`next_node`). The node that "ran out" is the one this call owns.
- Append is **after** the while loop (post-order), not inside it.
- Don't forget to **reverse**.

## Trace (the dead-end example)
`tickets = [["JFK","KUL"],["JFK","NRT"],["NRT","JFK"]]`
- JFK→KUL (smallest); KUL has no tickets → append KUL.
- back at JFK→NRT→JFK; drain → append JFK, NRT, JFK.
- `res = [KUL, JFK, NRT, JFK]` → reverse → `["JFK","NRT","JFK","KUL"]` ✅
- KUL was grabbed *first* but lands *last* — that's the reverse doing its job.
