---
title: Pinterest
tags: [company, pinterest, index]
---

# Pinterest — Interview Prep

> Curated view. Problems live in `problems/`, techniques in `patterns/`. This page just collects what's been tagged `pinterest`.

## Loop format (fill in as confirmed)
- Phone screen → onsite: coding × N + system design + behavioral / HM
- Coding skews **LC medium with follow-ups**. Recurring flavors below.

## Solved / in progress

| # | Problem | Technique | Status |
|---|---------|-----------|--------|
| 332 | [[reconstruct-itinerary\|Reconstruct Itinerary]] | [[hierholzer-eulerian-path\|Hierholzer / Eulerian path]] | ✅ |
| 465 | [[optimal-account-balancing\|Optimal Account Balancing]] | [[backtracking-concept-graph\|backtracking — partition]] | 🔜 |
| 282 | [[expression-add-operators\|Expression Add Operators]] | [[string-partition-backtracking\|string-partition backtracking]] | ✅ |
| 815 | Bus Routes | BFS shortest path | 🔜 |
| 1293 | [[shortest-path-obstacles-elimination\|Shortest Path w/ Obstacles Elimination]] | [[state-augmented-bfs\|state-augmented BFS]] | ✅ |
| 1055 | [[shortest-way-to-form-string\|Shortest Way to Form String]] | greedy subsequence (+ bisect) | ✅ |
| 2302 | [[count-subarrays-score-less-than-k\|Count Subarrays With Score Less Than K]] | sliding window (monotonic) | ✅ |

## Backlog / pattern siblings worth drilling

- 864 Shortest Path to Get All Keys — [[state-augmented-bfs]] (bitmask sibling of 1293)
- 131 Palindrome Partitioning — [[string-partition-backtracking]]
- 698 Partition to K Equal Sum Subsets — backtracking keystone → [[backtracking-concept-graph]]

## Patterns Pinterest seems to like

- **Edge-consuming graph traversal** — itineraries, pin pairings (Eulerian / [[hierholzer-eulerian-path|Hierholzer]])
- **Interval / sweep-line** — "engagements over time", pin visibility, count overlaps
- **Heap / top-k**, monotonic stack + binary search (windowed "best pin") → [[min-heap]]
- **Bipartite / union-find** — grouping unrelated images
- _expand as patterns repeat_
