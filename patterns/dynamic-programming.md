---
title: Dynamic Programming
tags: [pattern, dynamic-programming, dp, stub]
status: stub
---

# Dynamic Programming

## When to use it
Reach for DP when a problem asks for an **optimum** (min/max/count/"is it possible") and it has:
- **Overlapping subproblems** — the same smaller question is asked many times.
- **Optimal substructure** — the best answer to `n` is built from best answers to smaller inputs.

Signal phrases: "fewest / most," "number of ways," "can you reach," "longest / shortest," where a greedy choice isn't safe because a locally-worse step can lead to a globally-better result (e.g. [[coin-change]] — taking the biggest coin first can fail).

## How it works
1. **Define the state** in one sentence: `dp[i]` = the answer to the subproblem ending at / using `i`. Getting this sentence exact is 80% of the work.
2. **Base case** — the smallest, trivially-known state (seeds the table).
3. **Transition** — express `dp[i]` from earlier states.
4. **Order** — fill so dependencies are ready (bottom-up loop, or memoized recursion top-down).
5. **Answer** — read off the target cell.

```python
dp = [BASE_FILL] * (n + 1)
dp[0] = BASE_VALUE                       # smallest known case
for i in range(1, n + 1):
    dp[i] = OPT(dp[i - step] + cost      # combine from earlier states
               for step in choices if valid(i, step))
return dp[n]
```

## Gotchas
- **Sentinel direction must match the optimization.** Minimizing → unreachable = `+inf`; maximizing → `-inf`. A wrong-signed sentinel looks "optimal" and poisons the table (classic [[coin-change]] bug).
- **Base case off by a constant.** `dp[0]` is the foundation — `dp[0]=1` when it should be `0` shifts every answer.
- **Empty transition set.** If no choice is valid for a state, the `min`/`max` over an empty sequence crashes — supply a fallback (`... or [inf]`) or pre-fill the array with the sentinel.
- **Final unreachable check.** Compare the answer cell against the *sentinel* (`!= inf`), not `None`/`0`, before returning a "can't do it" result.
- **Reusable vs one-shot items.** Coin reuse (unbounded) vs each-item-once (0/1 knapsack) changes the loop order — know which you're in.

## Used in
- [[coin-change]] — `dp[i]` = fewest coins to make amount `i`; unreachable = `+inf`.
