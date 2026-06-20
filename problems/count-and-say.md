---
title: Count and Say
tags: [pinterest, problem, run-length-encoding, two-pointer, string, simulation]
leetcode: 38
difficulty: medium
technique: "[[run-length-encoding]]"
companies: [pinterest]
status: done
date: 2026-06-20
source: https://leetcode.com/problems/count-and-say/
---

# 38. Count and Say

## Approach
Pure simulation. Start at `"1"` and apply the "say" transform `n - 1` times. Each transform is a **run-length encode** of the current string: walk left to right, count consecutive equal chars, and emit `count` then the `char` (so `"1211"` → one `1`, one `2`, two `1` → `"111221"`).

Two equivalent ways to do the inner scan:
- **Two pointers** (latest code): `i` marks the start of a run, `j` runs forward while `s[j] == s[i]`; the run length is `j - i`, then jump `i = j`.
- **Running count**: compare each char to the previous one, `count += 1` on a match, flush `(count, prev)` on a change — plus one flush after the loop for the final run.

Both are [[run-length-encoding]]; the two-pointer version closes each run naturally (no separate trailing flush needed).

## Code
```python
class Solution:
    def countAndSay(self, n: int) -> str:
        s = '1'
        for _ in range(n - 1):
            i = 0
            res = []
            while i < len(s):
                j = i
                while j < len(s) and s[i] == s[j]:
                    j += 1
                res.append(str(j - i))   # run length
                res.append(s[i])         # the char
                i = j                    # jump to next run
            s = ''.join(res)             # collapse ONCE per full pass
        return s
```

## Key insight
**Rebuild `s` exactly once per pass — after the whole string is scanned, not after each run.** The `res` you accumulate during a pass *is* the next term; only join it back into `s` when the `while i < len(s)` loop finishes.

## Mistakes / gotchas
- **Mutating `s` mid-scan.** First version had `s = ''.join(res)` *inside* the inner loop, so `len(s)` and `s[i]`/`s[j]` changed while still being read — the scan corrupted itself and snowballed into garbage (`"1" → "11" → "1111" → "111121"...`). Fix: move the join outside the `while`.
- **(count-variable version) the trailing flush.** If you scan by comparing to the previous char, the last run never sees a "change," so it's never emitted inside the loop — you need an explicit `append(count); append(s[-1])` after the loop or you silently drop the final group.
- **Time complexity is exponential, not quadratic.** Each pass scans the current length `L_k`; total work is `L_1 + ... + L_n`. Lengths grow geometrically by ~Conway's constant (λ ≈ 1.3036), so the sum is dominated by the last term: **O(1.3ⁿ)** time and space. Saying "O(n²)" overcounts — early strings are tiny.

See also: [[run-length-encoding]] · [[two-pointer]]
