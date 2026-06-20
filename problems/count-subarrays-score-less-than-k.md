---
title: Count Subarrays With Score Less Than K
tags: [pinterest, problem, sliding-window, two-pointer, monotonic, binary-search, amortized]
leetcode: 2302
difficulty: hard
technique: "sliding window (monotonic left pointer)"
status: done
companies: [pinterest]
---

# 2302. Count Subarrays With Score Less Than K

> Pattern: **variable-size sliding window**. All elements positive ⇒ score is monotonic ⇒ a forward-only `left` pointer gives O(n). The recurring lesson: **when a pointer is already monotonic, two-pointer beats binary search.** (Same lesson, mirror image, as [[shortest-way-to-form-string|1055]].)

## Problem
`score(subarray) = (sum of elements) * (length)`. Count subarrays with `score < k`. `1 <= nums[i]` (all positive), `1 <= k`.

## Why sliding window works (the recognition signal)
All elements are **positive**, so:
- Extend the window right → score **strictly increases**.
- Shrink from the left → score **strictly decreases**.

That monotonicity is the whole game. For a fixed `right`, once `[left..right]` is the *longest* valid window (score `< k`), **every** start in `[left..right]` is also valid (shorter ⇒ smaller score). So this `right` contributes `right - left + 1` subarrays.

```
left = 0, window_sum = 0, count = 0
for right in range(n):
    window_sum += nums[right]
    while window_sum * (right - left + 1) >= k:   # shrink while INVALID
        window_sum -= nums[left]
        left += 1
    count += right - left + 1
```

## Gotchas (mine — all three bit me)
- **Score ≠ running weighted sum.** You can't reconstruct `sum*len` from length alone; the append-delta is `sum + x*(len+1)`, which *needs* the plain `sum`. Keep a plain `window_sum` and multiply by length where you test. (I first tried `+= (len+1)*x` — that's `1a+2b+3c`, a weighted sum, **not** `(a+b+c)*3`.)
- **Shrink order: read the departing element *before* bumping `left`.** Doing `left += 1` first makes you subtract the element that *stays*, not the one that *leaves*.
- **`>=` not `>`.** `score == k` is **not** `< k` → invalid → must shrink it out.

## Complexity — why it's O(n), not O(n²)
The `for` + inner `while` *looks* quadratic, but `left` only ever moves **forward** and never resets. Across the entire run it advances ≤ n times total. So total shrink work ≤ n → **O(n)** amortized, O(1) extra space.

---

## Variant A — maintain the score incrementally (what I actually wrote)
Instead of recomputing `window_sum * len`, carry the live **score** in `running_sum`. Correct, just bug-prone — the delta algebra is fiddly. Kept here because deriving it teaches the structure.

```python
class Solution:
    def countSubarrays(self, nums: List[int], k: int) -> int:
        left = running_sum = running_len = window_sum = res = 0
        n = len(nums)
        for right in range(n):
            # append delta: (L+1)*x + S   (running_len=L_old, window_sum=S_old here)
            running_sum += (running_len + 1) * nums[right] + window_sum
            window_sum  += nums[right]
            running_len += 1
            while running_sum >= k:
                window_sum  -= nums[left]                       # S -> S - y  (y = departing)
                running_sum -= nums[left] * running_len + window_sum  # = y*L + (S-y) = S + y*(L-1)
                running_len -= 1
                left += 1
            res += right - left + 1
        return res
```
- Append delta `S + x*(L+1)` ✓. Remove delta `S + y*(L-1)` ✓ — but **only** if `window_sum` is decremented first and `left` is bumped last.
- More moving parts than the top `score = window_sum * (right-left+1)` version. **In an interview, ship the simple multiply** — fewer places to get the off-by-one.

## Variant B — prefix sums + binary search (possible, but SLOWER)
You *can* binary-search the left boundary. `score(left, right)` strictly decreases in `left`, so it's searchable.

```python
P = [0]
for x in nums: P.append(P[-1] + x)        # window sum [l..r] = P[r+1]-P[l]
res = 0
for right in range(n):
    lo, hi = 0, right + 1                  # first left with score < k
    while lo < hi:
        mid = (lo + hi) // 2
        if (P[right+1] - P[mid]) * (right - mid + 1) < k:
            hi = mid
        else:
            lo = mid + 1
    res += right - lo + 1
return res
```
- **Hand-rolled** binary search, not `bisect_left` — the comparison key depends on `right`, so there's no static sorted array to call `bisect` on.
- **O(n log n)** — strictly worse than the O(n) sweep.

## The lesson: monotonic pointer ⇒ sweep beats bisect
Binary search is **stateless** between iterations — each `right` re-searches from scratch, re-paying `log n`, with no memory of where `left` landed last time. The sweep is **stateful** — `left` stays put and only crawls forward. That carried-over boundary is the entire savings; bisect throws it away.

> Two-pointer amortizes because `left` is monotonic; binary search re-pays `log n` each step because it can't reuse the previous boundary. Bisect shines when you *can't* sweep linearly — not here.

| Approach | Time | When it wins |
|----------|------|--------------|
| Sliding window (monotonic `left`) | **O(n)** | here — pointer is monotonic |
| Prefix + binary search | O(n log n) | when you can't sweep / left isn't monotonic |

See also: [[shortest-way-to-form-string]] (mirror lesson: bisect *helps* when you can't sweep) · [[pinterest|Pinterest hub]]
