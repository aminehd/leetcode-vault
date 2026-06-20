---
title: Min-heap / Priority Queue
tags: [pattern, heap, priority-queue, top-k]
status: stub
---

# Min-heap / Priority Queue

> A heap keeps the smallest (min-heap) or largest (max-heap) element at the root in O(1), with O(log n) push/pop. Use it whenever you repeatedly need "the best element so far" without sorting everything.

## When to reach for it

- **Top-k / k-th smallest** — keep a heap of size k.
- **Lexical / ordered traversal** — pop the smallest next choice (e.g. lexical Hierholzer in [[reconstruct-itinerary]]).
- **Windowed best** — running min/max over a stream.
- **Merge k sorted lists** — heap of the k front elements.

## Python

```python
import heapq

h = []
heapq.heappush(h, x)      # O(log n)
smallest = heapq.heappop(h)  # O(log n)
heapq.heapify(lst)        # O(n) build in place

# max-heap: push negatives
heapq.heappush(h, -x)
largest = -heapq.heappop(h)

# tuples sort by first element → (priority, item)
heapq.heappush(h, (priority, item))
```

## Gotchas

- `heapq` is a **min-heap only**. For max, negate values.
- Tuples compare element-by-element — if priorities tie, the *second* field must be comparable or you crash. Add a tiebreaker counter.
- Peeking is `h[0]` (no pop). Don't `heappop` just to look.

## Used in

- **332 Reconstruct Itinerary** — min-heap per node gives lexical-order edge consumption. → [[reconstruct-itinerary]]

Related: [[hierholzer-eulerian-path]]
