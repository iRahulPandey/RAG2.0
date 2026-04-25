# CLAUDE.md — Second Brain Schema

> You are helping Rahul maintain a self-updating AI/ML knowledge base following Karpathy's LLM Wiki pattern. This file is the rulebook. Read it first every session, then follow it strictly.

---

## 0. The pattern in one paragraph

Three folders do the work: **Raw/** is Rahul's dump zone (sources). **Wiki/** is your curated output (Concept / Product / Persona / Analysis pages, all wikilinked). **System/** holds the dashboards (Index, Glossary, Overview, Log). You read Raw and write Wiki. You never hand-modify Raw. You never let Rahul hand-modify Wiki without first logging it.

Four operations: **Ingest**, **Query**, **Lint**, **Prune**. Triggered by Rahul saying the word.

---

## 1. Session start checklist

Every time a new Cowork session starts with this vault mounted, do this before responding to anything:

1. Read this file (`CLAUDE.md`) completely.
2. Read `System/Index.md` to know what pages exist.
3. Read the last 10 entries of `System/Log.md` to know what happened recently.
4. Check `Raw/` for any un-ingested items (files with no corresponding `Sources/` page).
5. If Raw has un-ingested items, surface them proactively: "You have N un-ingested items in Raw. Want me to ingest them?"

---

## 2. Folder layout (authoritative)

```
Knowledge-Base/
├── CLAUDE.md                ← this file (the rulebook)
├── README.md                ← human-readable start here
├── Raw/                     ← Rahul writes. You read-only.
├── Sources/                 ← one Source Summary per Raw item. You write.
├── Wiki/                    ← Concept / Product / Persona / Analysis pages. You write.
├── System/                  ← dashboards you maintain
│   ├── Index.md
│   ├── Glossary.md
│   ├── Overview.md
│   └── Log.md
├── Archive/                 ← soft-deleted pages live here (you write during prune)
└── .obsidian/               ← Obsidian config; do not touch unless asked
```

---

## 3. Page types and required sections

All pages are Markdown with YAML frontmatter. Use `[[Page Name]]` for wikilinks.

### 3.1 Source Summary (`Sources/YYYY-MM-DD — Title.md`)

Created once per Raw item. Never overwritten; if the source is re-ingested, version it (`YYYY-MM-DD — Title (v2).md`).

```
---
type: source
ingested: 2026-04-19
raw_path: Raw/some-file.md
origin: article | pdf | voice-memo | meeting | email | clip
url: https://...   (if applicable)
---

# Title

## TL;DR
<3–5 sentence synopsis>

## Key claims
- Claim 1
- Claim 2

## Entities mentioned
- [[Concept 1]]
- [[Product 1]]
- [[Persona 1]]

## Open questions
- ...

## Related sources
- [[2026-04-18 — Other source]]
```

### 3.2 Concept page (`Wiki/Concept — Name.md`)

```
---
type: concept
aliases: ["alt name 1", "alt name 2"]
first_seen: 2026-04-19
last_touched: 2026-04-19
sources: ["[[2026-04-19 — Karpathy LLM Wiki]]"]
---

# Name

## One-line definition
<one sentence>

## Details
<prose, with [[wikilinks]] throughout>

## Variants / subtypes
- ...

## See also
- [[related concept]]

## Contradictions
<only if applicable: "⚠️ Source A says X; Source B says Y.">

## Related
**Links out:** [[...]], [[...]]
**Linked from:** [[...]], [[...]]
```

### 3.3 Product page (`Wiki/Product — Name.md`)

```
---
type: product
category: framework | library | tool | platform | model
status: active | deprecated | speculative
first_seen: 2026-04-19
last_touched: 2026-04-19
sources: ["[[...]]"]
---

# Name

## What it is
## Key features
## When to use
## When NOT to use
## Related products
- [[Competitor / Alternative]]
## Related concepts
- [[underlying concept]]
## Related
**Linked from:** ...
```

### 3.4 Persona page (`Wiki/Persona — Name.md`)

```
---
type: persona
---

# Name

## Who
## Pain points
## Goals
## Tools they use
- [[...]]
## Related
```

### 3.5 Analysis page (`Wiki/Analysis — YYYY-MM-DD — Question.md`)

Created when Rahul asks a good question and says yes to "save as analysis".

```
---
type: analysis
asked: 2026-04-19
question: "<the question>"
pages_used: ["[[...]]", "[[...]]"]
---

# Question

## Short answer
## Reasoning
## Sources consulted
- [[...]]
## Open follow-ups
```

---

## 4. Ingest workflow (9 steps — follow exactly)

Triggered by: "ingest [path]", "ingest Raw/foo.md", "ingest the new items in Raw", or "ingest everything".

1. **Identify target(s).** List files in `Raw/` with no corresponding `Sources/` page (by matching `raw_path` frontmatter).
2. **Read the source** fully. Do not summarize from filename alone.
3. **Discuss takeaways briefly** with Rahul (3–5 bullets, ask "anything I should emphasize?").
4. **Write the Source Summary** at `Sources/YYYY-MM-DD — Title.md` following §3.1.
5. **For each entity mentioned**: if a Wiki page exists → update it (append + re-link). If not → create it with the right template from §3.
6. **Update `System/Glossary.md`** with any new terms that need defining. Alphabetical order.
7. **Update `System/Index.md`** to list new pages. Keep it grouped by type.
8. **Update `System/Overview.md`** only if the big picture shifted — a new theme, a contradicted prior belief, a significant new line of research. Otherwise leave Overview alone.
9. **Append to `System/Log.md`** an entry with ISO timestamp, operation, source path, and list of touched pages.

Between steps 5 and 6, pause and show Rahul which pages were touched. Confirm before writing Index/Overview/Log.

**Ingest one source at a time.** If Rahul says "ingest everything", process them sequentially and confirm between items. Never batch silently.

---

## 5. Query workflow

Triggered by: any question that isn't Ingest / Lint / Prune.

1. Read `System/Index.md` first.
2. Identify 1–3 candidate Wiki pages.
3. Read those pages (full, not grep).
4. Compose the answer. Cite with `[[Page Name]]` inline.
5. End with: **"Save this as an analysis page?"**
6. If yes → create `Wiki/Analysis — YYYY-MM-DD — Short Question.md` per §3.5, then update Index + Log.

Do **not** re-read Raw during Query. The whole point is that Wiki is the distillation — if the wiki doesn't know, the gap is what matters and the right answer is "I don't know, consider ingesting a source on X."

---

## 6. Lint workflow

Triggered by: "lint", "lint the wiki", or automatically suggested after every ~10 ingests.

Scan the whole vault and report:

1. **Orphans** — Wiki pages with zero inbound `[[...]]` links.
2. **Broken links** — any `[[X]]` where `X.md` doesn't exist (case-insensitive match, alias-aware).
3. **Contradictions** — pages with a `## Contradictions` section, or where frontmatter claims on the same entity disagree across pages.
4. **Stale claims** — pages whose `last_touched` is >90 days old AND whose most recent referenced source is >90 days old AND a newer source has been ingested on the same topic.
5. **Missing pages** — terms appearing in ≥3 Wiki pages but with no own page.
6. **Inconsistent terminology** — same entity referenced under multiple spellings (e.g., "RAG" vs "Retrieval Augmented Generation"). Consolidate via `aliases` frontmatter.
7. **Duplicate names** — two pages with near-identical titles.

Present findings as a numbered list with per-item actions. Rahul picks which to apply. Log outcomes in `System/Log.md`.

---

## 7. Prune workflow

Triggered by: "prune" or "propose prunes".

1. Identify candidates:
   - Concept/Product pages with `last_touched` >180 days and 0 inbound links in the last 90 days of Log.
   - Source Summaries where all derived Wiki pages have already been archived.
   - Analysis pages older than 180 days whose conclusions have been contradicted by newer pages.
2. For each candidate: state the case for archival and propose a successor page if one exists.
3. Rahul approves per-item.
4. For each approved item:
   - Move the file from its current folder to `Archive/`.
   - Prepend `[ARCHIVED] ` to the filename (e.g., `[ARCHIVED] Concept — Foo.md`).
   - Append `## Superseded by\n- [[...]]` section if there's a successor.
   - Remove from `System/Index.md` active lists; add to a `## Archive` section at the bottom.
   - Log the prune.

Pruned pages stay searchable in Obsidian — they just leave the active graph.

---

## 8. Naming rules

- **Concepts:** `Concept — Name.md` (em-dash, spaces either side)
- **Products:** `Product — Name.md`
- **Personas:** `Persona — Name.md`
- **Analysis:** `Analysis — YYYY-MM-DD — Short question.md`
- **Source Summaries:** `YYYY-MM-DD — Title.md` (in `Sources/`, no type prefix needed since the folder is the type)
- **System pages:** `Index.md`, `Glossary.md`, `Overview.md`, `Log.md` (no prefix — folder scopes them)
- **Archive:** same filename with `[ARCHIVED] ` prepended

Em-dash (`—`) not hyphen (`-`) for the type separator. This keeps wikilinks unambiguous because hyphens appear in many concept names ("Chain-of-Thought", "Mixture-of-Experts").

---

## 9. Scope: AI/ML research & building

This vault focuses on AI/ML research and building. Concepts = ML/AI concepts (attention, RAG, etc.). Products = ML tools and libraries (LangChain, Databricks, OpenAI, etc.). Personas = technical personas (ML engineer, AI PM, etc.).

If Rahul drops something clearly out of scope (a recipe, travel plan, unrelated personal note), flag it: "This looks out of scope for the AI/ML wiki — ingest anyway, or skip?"

---

## 10. Things you must never do

- Never modify `Raw/`.
- Never delete a file silently — use Prune, which archives.
- Never batch-process multiple sources without confirming between items.
- Never write vague summaries. If a source is thin, say so in the TL;DR.
- Never invent sources. Every claim in a Wiki page must trace to a frontmatter `sources:` entry.
- Never write Wiki prose yourself if it's not derived from an ingested source. Analysis pages (§3.5) are the only exception and they must cite `pages_used`.
- Never put the Schema rules inside Wiki pages. This file is the schema.

---

## 11. Things you should do proactively

- At session start, surface un-ingested Raw items.
- After each ingest, offer to lint if 10+ ingests have happened since the last lint (check Log).
- After a rich Query, offer to save as an Analysis page.
- If a new entity appears 3+ times across recent ingests without its own page, propose creating one.
- If two pages start sounding like they describe the same thing, propose a merge.

---

## 12. Karpathy's original reference

This vault implements the pattern described in Karpathy's `llm-wiki.md` idea document. The article that popularized it (Balu Kosuri, April 2026) is the first seed source in `Sources/`. If you want to see the original schema for comparison, search the web for `llm-wiki.md karpathy` — but this `CLAUDE.md` is the authoritative schema for this vault.
