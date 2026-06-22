---
title: Longest Word in Dictionary
tags: [pinterest, problem, sorting, hash-set, incremental-construction, string]
leetcode: 720
difficulty: medium
technique: "[[sorting]]"
companies: [pinterest]
status: done
date: 2026-06-22
source: https://leetcode.com/problems/longest-word-in-dictionary/
---

# 720. Longest Word in Dictionary

## Approach
Find the longest word that can be built **one letter at a time**, where every intermediate prefix is itself a word in the list. Tie → lexicographically smallest.

The clean trick is **sort + a `set`**, no trie needed:
1. `words.sort()` — lexicographic order guarantees a word's one-shorter prefix `w[:-1]` is processed **before** `w` (a string always sorts before any string that extends it).
2. Keep a `buildable` set of confirmed words. For each `word`, check the **single** thing: is `word[:-1]` already in `buildable`? (Length-1 words pass automatically.) If yes, it's buildable → add it.
3. Track `ans`, replacing only when **strictly longer** — so the lex-smallest of each length (which sort visits first) wins the tie for free.

## Code
```python
class Solution:
    def longestWord(self, words: List[str]) -> str:
        buildable = set([])
        words.sort()
        max_len = 0
        ans = ''
        for word in words:
            if len(word) == 1 or word[:-1] in buildable:
                buildable.add(word)
                if len(word) > max_len:
                    max_len = len(word)
                    ans = word
        return ans
```
**Complexity:** `O(N·L·log N)` time (sort dominates — each comparison is up to `L` chars), `O(N·L)` space for the set.

## Key insight
**Sorting makes every prefix appear before its extensions**, so you only ever check **one** prefix (`w[:-1]`) instead of all of them — the shorter prefixes were validated on earlier iterations and folded into `buildable`. Sort order also delivers the lexicographic tie-break for free, as long as you replace `ans` only on *strictly greater* length.

## Mistakes / gotchas
- **Reaching for a trie first.** A trie works but it's heavier and easy to fumble; sort + set is ~6 lines, same asymptotics. A trie only earns its keep when you need *repeated prefix queries* (autocomplete, "count words with prefix X") — not a one-pass scan like this.
- **"Set lookup is O(1)" — half-true.** O(1) in the *number of keys*, but O(`L`) in *key length* because hashing a string reads all its characters. So the pass is O(N·L), not O(N). Good nuance to volunteer in an interview.
- **Tie-break direction.** Must update on `len(word) > max_len` (strictly greater), not `>=` — otherwise a later, lexicographically-larger word of equal length would overwrite the correct answer.
- **It's not about leaves.** A buildable word is usually *not* a trie leaf; the condition is "every node on the root→word path is itself an end-of-word," i.e. every prefix is present.

See also: [[sorting]] · [[trie]]
