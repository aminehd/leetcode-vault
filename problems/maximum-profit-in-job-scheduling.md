---
title: Maximum Profit in Job Scheduling
tags: [pinterest, problem, dynamic-programming, binary-search, weighted-interval-scheduling]
leetcode: 1235
difficulty: hard
technique: "[[dynamic-programming]]"
companies: [pinterest]
status: done
date: 2026-06-21
source: https://leetcode.com/problems/maximum-profit-in-job-scheduling/
---

# 1235. Maximum Profit in Job Scheduling

## Approach
Weighted interval scheduling. Sort jobs by **start time**, then DP backward. `dp[i]` = best profit using jobs `i, i+1, …, n-1`. For each job, two choices:
- **skip it** → `dp[i+1]`
- **take it** → `profit_i + dp[idx]`, where `idx` is the first job whose `start >= end_i`

Because jobs are sorted by start, that next-compatible job is found with a **binary search** over the start-times array (`bisect_left(starts, end_i, i+1)`), and since we fill `dp` right-to-left, `dp[idx]` is already computed. Answer is `dp[0]` (= `max(dp)`).

This is [[dynamic-programming]] + [[binary-search]] — the binary search is what turns the naive O(n²) "scan for the next job" into O(n log n).

## Code
```python
from bisect import bisect_left   # don't forget this import
from typing import List

class Solution:
    def jobScheduling(self, startTime: List[int], endTime: List[int], profit: List[int]) -> int:
        jobs = list(zip(startTime, endTime, profit))
        jobs.sort(key=lambda x: x[0])           # sort by start time
        n = len(profit)
        dp = [0] * (n + 1)                      # dp[n] = 0 base case
        starts = [job[0] for job in jobs]       # hoisted ONCE, not per-iteration
        for i in range(n - 1, -1, -1):
            dont_take = dp[i + 1]
            start_time, end_time, p = jobs[i]
            idx = bisect_left(starts, end_time, i + 1)   # first job with start >= end_time
            take_i = p + dp[idx]                 # dp[idx] valid even when idx == n (dp[n]=0)
            dp[i] = max(take_i, dont_take)
        return max(dp)                           # == dp[0]
```
**Complexity:** sort `O(n log n)` + n iterations × one `bisect` `O(log n)` → **O(n log n)** time, `O(n)` space.

## Key insight
**Sort by start time so the next compatible job lives in the suffix — then binary-search for it.** Filling `dp` backward means `dp[idx]` for that later job is already done. The `lo=i+1` bound on `bisect` restricts the search to jobs after `i`, which is exactly where any job with `start >= end_i` can be (everything before has a smaller start).

`return max(dp)` is valid because `dp[i] = max(take_i, dp[i+1]) ≥ dp[i+1]` — the array is non-increasing, so `dp[0]` is the max. `return dp[0]` is the cleaner O(1) version.

## Mistakes / gotchas
- **`if idx < n:` guard dropped the last job's profit.** `dp` has size `n+1` with `dp[n]=0`, so `idx == n` is legal ("take this job, nothing after"). Guarding it out forfeits that profit. Just do `take_i = p + dp[idx]` unconditionally.
- **`return dp[-2]` (= `dp[n-1]`) returned only the last job's subproblem.** The full answer is `dp[0]` / `max(dp)`.
- **Rebuilding `[job[0] for job in jobs]` inside the loop** made it O(n²). Hoist `starts` above the loop → O(n log n).
- **Shadowing the `profit` parameter** with `start, end, profit = jobs[i]` (a scalar) — harmless here since `n` is already computed, but rename to `p` to avoid the smell.
- **Forgetting `from bisect import bisect_left`** — `bisect_left` isn't a builtin.

See also: [[dynamic-programming]] · [[binary-search]]
