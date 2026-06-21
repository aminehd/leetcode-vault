---
title: Binary Search
tags: [pattern, binary-search, bisect, stub]
status: stub
---

# Binary Search

## When to use it
Two flavors:
1. **Search a sorted array** — find an element, or the first/last position satisfying a monotone predicate (`bisect`). Signal: data is sorted (or you can sort it) and you need a position/boundary in `O(log n)`.
2. **Binary search on the answer** — the answer lives in a numeric range and "is `x` feasible?" is **monotone** (once true, stays true). Signal: "minimize the maximum…", "largest `k` such that…", "smallest capacity/speed that works."

In [[maximum-profit-in-job-scheduling]] it's flavor 1: jobs sorted by start time, so the next compatible job (first `start >= end_i`) is found with `bisect_left` instead of a linear scan — O(n log n) instead of O(n²).

## How it works
Maintain a `[lo, hi)` window and halve it each step toward the boundary.

```python
from bisect import bisect_left, bisect_right

# flavor 1: first index with arr[i] >= target, searching a suffix
idx = bisect_left(arr, target, lo)        # arr sorted; idx in [lo, len(arr)]

# flavor 2: binary search on the answer
lo, hi = min_ans, max_ans
while lo < hi:
    mid = (lo + hi) // 2
    if feasible(mid):      # monotone predicate
        hi = mid           # mid works → try smaller
    else:
        lo = mid + 1       # mid fails → go bigger
return lo                  # smallest feasible answer
```

## Gotchas
- **`bisect_left` vs `bisect_right`** — `left` lands *on* an equal element (first `>=`), `right` lands *after* it (first `>`). Picking wrong is a silent off-by-one (see [[shortest-way-to-form-string]]).
- **Loop invariant / boundary**: be deliberate whether the window is `[lo, hi]` or `[lo, hi)`; mismatching it with `hi = mid` vs `mid - 1` causes infinite loops or skipped answers.
- **`mid = (lo + hi) // 2`** biases low — fine for "smallest feasible"; for "largest feasible" you often need `mid = (lo + hi + 1) // 2` to avoid looping.
- **Monotonicity is required.** Flavor 2 only works if `feasible` flips once (false→true or true→false). Verify it before trusting the search.
- **It can be the wrong tool.** When a pointer is already **monotonic**, a linear two-pointer sweep is O(n) and beats re-searching O(n log n) — bisect throws away the carried-over boundary (see [[count-subarrays-score-less-than-k]]).

## Used in
- [[maximum-profit-in-job-scheduling]] — `bisect_left` over start times to find the next non-overlapping job.
