---
title: Sorting
tags: [pattern, sorting, preprocessing, stub]
status: stub
---

# Sorting

## When to use it
Sorting is rarely the *answer* — it's the **preprocessing step that creates an invariant** the rest of your algorithm exploits. Reach for it when:
- The problem says "smallest/largest," "in order," "k-th," or "lexicographically smallest."
- A useful property only holds in order — e.g. **prefixes sort before their extensions**, intervals line up by start/end, duplicates become adjacent, or a greedy/two-pointer sweep becomes valid.
- You want to turn an O(n²) "search for the matching element" into O(n log n) sort + O(n)/O(log n) lookup.

Tell: "if these were sorted, the next thing I need would always be right *there*."

## How it works
Sort, then ride the ordering with a single pass (or binary search).

```python
items.sort(key=lambda x: x.key)     # or sort by a tuple for tie-breaks
state = init
for x in items:                     # ordering guarantees what you need is ready
    use(x, state)                   # e.g. prev item already processed
    update(state, x)
```

Python notes: `list.sort()` is in-place (`O(n log n)`, stable); `sorted()` returns a copy. Sort by a **tuple** `key=lambda x: (x.len, x.name)` for primary/secondary ordering — stability then preserves earlier order on ties.

## Gotchas
- **String comparisons aren't O(1).** Each compare is O(L) in length, so sorting N strings is `O(N·L·log N)`, not `O(N log N)`.
- **Tie-break direction.** If you want the lexicographically smallest among equals, sort ascending and update your answer only on a *strict* improvement (`>`), so the first (smallest) survives.
- **Sorting destroys original indices.** If you need them, sort `(value, index)` pairs or `argsort`.
- **Don't over-sort.** If you only need the k smallest, a heap (`O(n log k)`) or quickselect (`O(n)`) beats a full sort.
- **Stability matters** when sorting by a secondary key in two passes — rely on it, or fold both keys into one tuple.

## Used in
- [[longest-word-in-dictionary]] — sort so every word's prefix `w[:-1]` is processed before it; one set-lookup per word.
- [[maximum-profit-in-job-scheduling]] — sort jobs by start time so the next compatible job lives in the suffix (then binary-search it).
