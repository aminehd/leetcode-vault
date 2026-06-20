---
title: Optimal Account Balancing
tags: [pinterest, problem, backtracking, partition, dfs]
leetcode: 465
difficulty: hard
technique: "[[backtracking-concept-graph|backtracking — partition]]"
companies: [pinterest]
status: todo
---

# 465. Optimal Account Balancing

> Pattern: **backtracking / partition** — reduce people to net balances, then settle debts with the fewest transactions. See the family in [[backtracking-concept-graph]].

## Problem

Given a list of transactions `[from, to, amount]`, return the **minimum number of transactions** to settle everyone's debt.

## Key idea

1. Collapse transactions into a **net balance per person** (a dict, then drop zeros).
2. The remaining nonzero balances must sum to 0. Find the min number of transfers to zero them all out.
3. **Backtrack:** fix the first nonzero balance `bal[i]`, settle it against every opposite-sign partner `bal[j]`, recurse, undo. Track the minimum.

```python
def minTransfers(transactions):
    net = {}
    for f, t, a in transactions:
        net[f] = net.get(f, 0) - a
        net[t] = net.get(t, 0) + a
    bal = [v for v in net.values() if v != 0]

    def dfs(i):
        while i < len(bal) and bal[i] == 0:
            i += 1
        if i == len(bal):
            return 0
        best = float('inf')
        for j in range(i + 1, len(bal)):
            if bal[j] * bal[i] < 0:          # opposite sign → can settle
                bal[j] += bal[i]
                best = min(best, 1 + dfs(i + 1))
                bal[j] -= bal[i]             # undo
        return best

    return dfs(0)
```

## Why it's the goal of the partition family

Buckets form **implicitly**: each "settle against partner" is choosing which group a debt closes into. A clean cancel = a zero-sum group closing. Same choose/explore/un-choose skeleton as subset-sum (698) — see [[backtracking-concept-graph]].

See also: [[backtracking-concept-graph]] · [[hierholzer-eulerian-path]]
