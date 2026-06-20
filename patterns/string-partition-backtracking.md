---
tags: [pattern, backtracking, string-partition, dfs]
problems: [139, 140, 131, 93, 282]
---

# String-Partition Backtracking — "fix start, slide end"

## When to reach for it (the recognition signal)
**You must cut a string/array into *contiguous* pieces, and each choice is "how long is the next piece?"**
The "items" you place aren't single elements — they're variable-length chunks carved off the front. The moment you think *"where do I cut?"*, this is the pattern.

It's the substring sibling of the subset/partition family (see [[backtracking-concept-graph]]): same choose/explore/un-choose skeleton, but a choice = a *prefix slice* instead of one element.

## The move (memorize this shape)
```python
def dfs(index, ...state...):        # index = start of the unconsumed suffix
    if index == n:                  # consumed everything → check / record
        ...
        return
    for i in range(index + 1, n + 1):   # i = END of the next piece (exclusive)
        piece = s[index:i]              # carve it: index is fixed, i slides
        if not valid(piece):            # problem-specific test (and often `break`/`continue`)
            ...
        dfs(i, ...update state with piece...)   # next piece starts where this ended
```
- **`index`** = locked, the first char of the next piece (always included).
- **`i`** = the slider, how many chars this piece eats: `index+1` (shortest) … `n` (rest).
- **recurse on `i`** — half-open slice `s[index:i]` includes `index`, excludes `i`, and `i` becomes the next start.
- `index == n` = "off the end, all consumed" → base case.

## The only thing that changes per problem: `valid(piece)` + what you accumulate
- **139 Word Break** — `piece in wordDict`? (can memoize on `index`)
- **140 Word Break II** — same, collect the sentence string
- **131 Palindrome Partitioning** — `piece == piece[::-1]`?
- **93 Restore IP Addresses** — `piece` is 0–255, no leading zero, and exactly 4 pieces
- **282 Expression Add Operators** — `piece` = an operand (no leading zero); carry `total`, `prev_op` for `*`. → [[expression-add-operators]]

## Gotchas
- **Slice is `s[index:i]`** (colon!), start at `index`, not `index+1`.
- **Leading-zero / validity** often lets the single char through but rejects longer (`break` once `s[index]=='0'`).
- **Base case returns on `index == n` unconditionally**, else you slice `s[n:n]=""`.
- **First piece sometimes special** (282: no leading operator).
- **Don't value-prune unless monotonic** — 282's `total` isn't monotonic (`-`,`*`), so over-shoot pruning is wrong.
- **`path` as a passed string** = free backtracking (immutable, no undo line). Mutable list ⇒ append/pop.

## One-liner to lock it
> Subsets picks *elements*; string-partition picks *prefixes*. Same tree — fix the start, vary the cut, recurse on the cut.

See also: [[backtracking-concept-graph]] · [[expression-add-operators]] · [[hierholzer-eulerian-path]]
