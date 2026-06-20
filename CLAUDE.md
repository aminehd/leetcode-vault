# LeetCode Vault — structure & conventions

**Read this before adding or moving any note.** This vault is **flat-by-type, company-as-tag**. Every note lives in exactly one folder so wikilinks are always unambiguous.

## Folders

| Folder | Holds | Filename rule |
|--------|-------|---------------|
| `problems/` | one note per problem | clean slug, e.g. `reconstruct-itinerary.md` (LeetCode # in frontmatter, **not** the filename) |
| `patterns/` | reusable techniques (shared across companies) | slug, e.g. `state-augmented-bfs.md` |
| `prep/` | concept maps, study plans | slug |
| `companies/` | one curated index page per company | `pinterest.md` |

Do **not** create `companyX/problems/` or `companyX/patterns/` subfolders. Company is a tag, not a folder.

## Rules (must follow)

1. **Wikilinks are bare:** `[[reconstruct-itinerary]]`, never `[[problems/reconstruct-itinerary]]`. Filenames are globally unique so Quartz resolves them from anywhere.
2. **A new problem** goes in `problems/<slug>.md` with this frontmatter:
   ```yaml
   ---
   title: <Human Title>
   tags: [<company>, problem, <pattern-tags...>]
   leetcode: <number>
   difficulty: easy|medium|hard
   technique: "[[<pattern-slug>]]"
   companies: [<company>, ...]
   status: todo|done
   ---
   ```
3. **A new pattern** goes in `patterns/<slug>.md`. Link problems to it via `technique:` and a "Used in" list.
4. **Surfacing on a company page:** add the company to both `tags:` and `companies:` in the problem's frontmatter, then add a row to `companies/<company>.md`. Never copy the problem file.
5. **No empty folders, no `raw/`.** Raw scrape dumps live under `apps/pinterest-practice/raw/`, not in the vault.

## After any change

Verify every wikilink resolves (filenames are the link targets). The Quartz site discovers content dynamically — no hardcoded page lists — so renames/moves only require fixing the links that point at them.

## For the practice app

A machine-readable copy of this convention is in `_structure.json` (same folder). When the app writes a session/problem note into the vault, follow the folder + frontmatter rules above.
