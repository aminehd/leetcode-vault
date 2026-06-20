---
title: Shortest Way to Form String
tags: [pinterest, problem, greedy, subsequence, two-pointer, binary-search, bisect]
leetcode: 1055
difficulty: medium
technique: "greedy subsequence matching (+ bisect speedup)"
status: done
companies: [pinterest]
---

# 1055. Shortest Way to Form String

> Pattern: **greedy subsequence matching** — sweep `source` repeatedly, consume `target` as far as you can each pass. Min passes = answer. Optional **bisect** speedup turns each "find next char" into a binary search.

## Problem
Given `source` and `target`, return the **minimum number of subsequences of `source`** whose concatenation equals `target`. Return `-1` if impossible (i.e. `target` has a character `source` never contains).

## Key insight
A *subsequence* lets you skip characters in `source` — so **never break on a mismatch**, just keep walking source for the next needed char. One full pass through `source` consumes "as much of `target` as a single copy can." Count the passes.

**The `-1` condition:** if a whole pass moves `target`'s pointer by **zero**, `source` is missing a char `target` needs → no number of copies ever helps → `-1`. (Equivalently: any char of `target` not in `source`.)

---

## Solution 1 — Greedy two-pointer (no bisect)  ·  O(n·m)
Each pass re-scans all of `source`; at most `n` passes (every pass advances `i` by ≥1).

```python
class Solution:
    def shortestWay(self, source: str, target: str) -> int:
        n = len(target)
        res = 0
        i = 0                          # pointer into target
        while i < n:
            j = i                      # snapshot before this pass
            for c in source:           # one full sweep of source
                if i < n and target[i] == c:
                    i += 1             # advance only on a match
            if j == i:                 # zero progress this pass → impossible
                return -1
            res += 1                   # one more copy of source used
        return res
```
- `i` walks `target`; the inner `for` walks one copy of `source`.
- `j == i` after a pass ⇒ nothing matched ⇒ `-1`.
- **Faster in practice on LeetCode** — see the note at the bottom.

---

## Solution 2 — Preprocess + binary search  ·  O(m + n·log m)
Build, for each character, the sorted list of its positions in `source`. To match the next `target` char, **binary-search the first position ≥ cursor** instead of re-scanning.

```python
from collections import defaultdict
from bisect import bisect_left

class Solution:
    def shortestWay(self, source: str, target: str) -> int:
        idx = defaultdict(list)
        for i, c in enumerate(source):
            idx[c].append(i)           # positions land already sorted

        cursor = 0                     # next usable index in current copy of source
        res = 0                        # counts RESTARTS (extra copies)
        for t in target:
            if t not in idx:
                return -1              # char never appears → impossible
            candids = idx[t]
            j = bisect_left(candids, cursor)   # first position >= cursor
            if j >= len(candids):              # none left in this copy → new copy
                res += 1
                cursor = candids[0] + 1
            else:
                cursor = candids[j] + 1
        return res + 1                 # +1 for the first copy (never counted)
```

### Two things that confused me (and the fixes)
- **Two coordinate systems.** `cursor` indexes into **source**; `j` indexes into **candids** (a list of source-positions). Once separated, the "can they be equal?" worry dissolves: `cursor` *is* a source-index, `candids` *holds* source-indices, so they can be dead-equal — and we **want** that match.
- **Why `bisect_left`, not `bisect_right`.** `cursor` is the *next index I'm allowed to use* — `cursor` itself is fair game, so I want the first position **≥ cursor**. `bisect_left` lands *on* an equal element; `bisect_right` skips past it.
  - `source="ab", target="ab"`: match `a` → `cursor=1`; match `b` → `candids=[1]`, `cursor=1` **equal**. `bisect_left([1],1)=0` uses it ✓. `bisect_right([1],1)=1==len` → false restart → wrong answer `2` ✗.
  - Equality is the *normal* case (adjacent chars), not an edge case. `bisect_left` lets you **not reason about it at all** — just "first index ≥ cursor."
- **Why `res + 1`.** `res` only counts *restarts* (exhausting a copy). The first copy was free and never counted, so total copies = restarts + 1. (Alt bookkeeping: start `res=1` and `+=1` on restart.)

---

## ⚠️ Bisect is *slower* here — and why that's the real lesson
On LeetCode the bisect version runs **slower** than the plain two-pointer. Not a fluke:
- **Tiny constraints** (`|source|, |target| ≤ 1000`): O(n·m) ≤ 10⁶ ops, and usually far less (few passes). The log-factor win only shows up when `source` is *huge*.
- **Constant factors flip it.** Bisect pays per char for a dict membership check, a dict lookup, and a `bisect_left` **function call** — expensive in CPython vs a tight `target[i] == c` compare. Plus building `idx` upfront.

**Interview-gold framing:** ship the simple O(n·m), then say *"if `source` were large, or I matched many targets against the same source, I'd preprocess into a per-char position index and binary-search — O(m + n·log m) — amortizing the build across queries."* The **many-targets / one-source** case is where bisect genuinely pays: build `idx` once, reuse it.

## Complexities
| Solution | Preprocess | Match | Note |
|----------|-----------|-------|------|
| Two-pointer | — | O(n·m) | wins at small n,m (LeetCode) |
| Bisect | O(m) | O(n·log m) | wins for large source / repeated queries |

See also: [[state-augmented-bfs]] (other "consume a resource" greedy/BFS) · [[pinterest|Pinterest hub]]
