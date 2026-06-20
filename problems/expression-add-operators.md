---
title: Expression Add Operators
tags: [pinterest, problem, backtracking, string-partition, dfs]
leetcode: 282
difficulty: hard
technique: "[[string-partition-backtracking]]"
status: done
companies: [pinterest]
---

# 282. Expression Add Operators

> Pattern: **string-partition backtracking** — fix `index`, slide `i`, carve `num[index:i]`. See [[string-partition-backtracking]].

## Problem
Insert `+ - *` (or nothing, to make multi-digit numbers) between the digits of `num` so the expression evaluates to `target`. Return all such expressions.

## The decision that confused me (and the fix)
**"Why fix `index` and loop `i` from `index+1`?"** Because the next *operand* is a variable-length chunk, not one digit. So you carve it off the front:
```python
for i in range(index + 1, n + 1):   # i = end of this operand (exclusive)
    next_num = num[index:i]          # operand = num[index:i]   (index = its first digit, always included)
    ... dfs(i, ...)                  # next operand starts where this ended
```
- `index` = start of the operand (fixed — it's the first digit, you always include it).
- `i` = how many digits this operand eats (the slider): `index+1` (one digit) … `n` (all remaining).
- recurse on `i` (half-open slice: `num[index:i]` includes `index`, excludes `i`).
This is the generic **string-partition** move — same as Word Break / Palindrome Partitioning.

## State carried
`dfs(index, path, prev_op, total)`
- `index` – next position to consume
- `path` – expression string so far (passed; immutable ⇒ free backtrack, no undo line)
- `total` – current evaluated value
- `prev_op` – **last operand, signed** — the secret for `*` precedence

## The `*` precedence trick
Evaluating left-to-right, but `*` binds tighter than the `+`/`-` already applied. So **rewind the last operand and reapply it multiplied**:
```
new_total = total - prev_op + prev_op * cur
new_prev  = prev_op * cur
```
- `+cur` → prev_op = `+cur`
- `-cur` → prev_op = `-cur`   ← signed, so `5 - 3 * 4` works
- `*cur` → prev_op = `prev_op * cur`  (chains: `2*3*4` keeps the running product as prev_op)
Invariant: **`prev_op` = the last multiplicative term sitting in `total` that a new `*` must undo.** One formula handles `*` after `+`, `-`, or `*`.

## Code
```python
class Solution:
    def addOperators(self, num: str, target: int) -> List[str]:
        n, res = len(num), []
        def dfs(index, path, prev_op, total):
            if index == n:
                if total == target:
                    res.append(path)
                return                                   # ← return on ALL digits consumed
            for i in range(index + 1, n + 1):
                next_num = num[index:i]
                cur = int(next_num)
                if len(next_num) > 1 and next_num[0] == '0':   # no leading zeros
                    break
                if index == 0:
                    dfs(i, next_num, cur, cur)                 # first operand: NO operator
                else:
                    dfs(i, path+'+'+next_num, cur, total + cur)
                    dfs(i, path+'-'+next_num, -cur, total - cur)
                    dfs(i, path+'*'+next_num, prev_op*cur, total - prev_op + prev_op*cur)
        dfs(0, '', 0, 0)
        return res
```

## Gotchas (mine)
- **NO value pruning.** I added `if total > target: return` — WRONG. `-` and `*` can bring an overshot total back down, so e.g. `10-5` (target 5) gets killed after building `10`. Only accept-test at `index == n`.
- **First operand is special** (`index == 0`): no leading operator, or you emit `*1`, `+2`, etc. (operators are binary — need a left operand).
- **Base case returns on `index == n` unconditionally** — else the loop slices `num[n:n] = ""` → `int("")` crash.
- **Slice is `num[index:i]`** with a colon, start at `index` (not `index+1`).
- `prev_op` must be **signed** for `-`.

## Trace `num="123", target=6` → `["1+2+3", "1*2*3"]`
Building `1*2*3`: after `1*2`, total=2, prev_op=2; then `*3`: `2 - 2 + 2*3 = 6`, prev_op=6. ✓

See also: [[string-partition-backtracking]] · [[backtracking-concept-graph]]
