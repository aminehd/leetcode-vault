---
title: Coin Change
tags: [pinterest, problem, dynamic-programming, bottom-up, unbounded-knapsack]
leetcode: 322
difficulty: medium
technique: "[[dynamic-programming]]"
companies: [pinterest]
status: done
date: 2026-06-20
source: https://leetcode.com/problems/coin-change/
---

# 322. Coin Change

## Approach
Bottom-up 1-D DP over amounts. `dp[i]` = **fewest coins to make amount `i`**. Base case `dp[0] = 0` (zero coins make zero). For each amount `i` from `1..amount`, try every coin `x` that fits (`x <= i`) and take the cheapest predecessor plus one:

```
dp[i] = min(dp[i - x] + 1  for every coin x <= i)
```

If no coin fits (or every predecessor is unreachable), `dp[i]` stays `+inf` = "can't make this amount." At the end, `dp[amount]` is the answer, or `-1` if it's still `inf`. This is the unbounded-knapsack shape (coins reusable). See [[dynamic-programming]].

## Code
```python
from typing import List

class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [0] * (amount + 1)
        for i in range(1, amount + 1):
            dp[i] = min([dp[i - x] + 1 for x in coins if x <= i] or [float('inf')])
        return dp[amount] if dp[amount] != float('inf') else -1
```
- `[... ] or [float('inf')]` — the `or` supplies a fallback list when no coin fits, so `min` never crashes on an empty sequence.
- **Time** `O(amount × len(coins))`, **space** `O(amount)`.

## Key insight
**Your DP sentinel must point the same direction as your optimization.** You're taking a `min`, so "unreachable" has to be `+inf` — the value `min` runs *away* from. Then real paths always beat it and unreachability propagates cleanly (`inf + 1 = inf`). Flip to a `max` problem and the sentinel flips to `-inf`.

## Mistakes / gotchas
- **`max` instead of `min`, and no `+1`.** First pass optimized the wrong direction and forgot to count the coin just spent. `dp[i] = min(dp[i-x] + 1)` — the `+1` is the coin used to step from `i-x` to `i`.
- **`dp[0] = 1`.** Base case is `dp[0] = 0`: zero coins make amount zero. Seeding it wrong throws the whole table off by a constant.
- **`-inf` sentinel poisoned the table.** With a `min`, `-inf` looks like the *best* option, so unreachable amounts got picked and spread. Trace `coins=[2], amount=3`: `dp[1] = -inf` → `dp[3] = -inf + 1 = -inf` → wrongly returns `-inf`. Fix: use `+inf`.
- **`dp[amount] != None` never fires.** The array holds floats, never `None`, so the guard was always true. Compare against the actual sentinel: `!= float('inf')`, else return `-1`.

See also: [[dynamic-programming]]
