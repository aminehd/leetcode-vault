---
title: Trie (Prefix Tree)
tags: [pattern, trie, prefix-tree, string, stub]
status: stub
---

# Trie (Prefix Tree)

## When to use it
A tree where each node is one character and root→node paths spell prefixes. Reach for it when you have **many words and repeated prefix queries against the same dictionary**:
- Autocomplete / "all words with prefix `app`."
- "Does any word start with…", "count words with prefix X."
- Word-search / board-DFS where you prune branches by valid prefixes (e.g. Word Search II).
- Bitwise tries for max-XOR problems.

Tell: you'll query prefixes **over and over** — build once, query many times. If you only touch each word once (like [[longest-word-in-dictionary]]), a **sort + `set`** is simpler and same complexity; the trie's build cost won't pay off.

## How it works
Each node holds a map `char → child` and an `is_end` flag. Insert walks/creates nodes char by char; search walks them.

```python
class Trie:
    def __init__(self):
        self.root = {}                      # nested dicts; '$' marks end-of-word

    def insert(self, word):
        node = self.root
        for c in word:
            node = node.setdefault(c, {})
        node['$'] = True

    def search(self, word, prefix=False):
        node = self.root
        for c in word:
            if c not in node:
                return False
            node = node[c]
        return True if prefix else '$' in node
```
Insert/search are **O(L)** in word length, independent of how many words are stored.

## Gotchas
- **Mark end-of-word.** Without an `is_end`/`'$'` flag you can't tell a stored word from a mere prefix ("app" vs the prefix of "apple").
- **Over-engineering.** If each word is touched once, a `set` (+ sort for ordering) beats it — fewer lines, fewer bugs.
- **Memory.** A node-per-char tree is heavy; nested dicts are simplest in Python, arrays of size 26 are faster but bulkier.
- **DFS order for lexicographic results.** Iterate children in sorted key order when you need smallest-first output.
- **"Buildable/valid path" ≠ leaf.** Conditions usually live on *every node along the path* (e.g. each is end-of-word), not on leaves.

## Used in
- [[longest-word-in-dictionary]] — works, but sort + `set` is the lighter call; trie pays off only with repeated prefix queries.
