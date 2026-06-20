# LeetCode Vault

Interview patterns, problem walkthroughs, and visual summaries.

## Structure

Flat-by-type, company-as-tag. Every note lives in exactly one place, so wikilinks are always unambiguous.

- `problems/` — one note per problem. Clean slug filename (e.g. `reconstruct-itinerary.md`); the LeetCode number lives in frontmatter (`leetcode: 332`) and the H1.
- `patterns/` — reusable technique notes (Hierholzer, state-augmented BFS, min-heap…), shared across companies.
- `prep/` — concept maps and study plans.
- `companies/` — curated index pages (e.g. `pinterest.md`). A problem is **tagged** with its companies, never copied into a company folder.

## Conventions

- **Wikilinks are bare:** `[[reconstruct-itinerary]]`, never `[[problems/reconstruct-itinerary]]`. Filenames are unique so Quartz resolves them anywhere.
- **Company = frontmatter tag.** Add `companies: [pinterest]` (and the `pinterest` tag) to a problem to surface it on the company page.
- Start from `_index.md` (the home page) or any company page.
