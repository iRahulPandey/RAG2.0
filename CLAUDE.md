# CLAUDE.md — RAG2.0

## What this is

A pedagogical Jupyter notebook (`walkthrough.ipynb`) that races three RAG architectures head-to-head on the same PDF:

1. **Classic Vector RAG** — Docling → chunk → sentence-transformers → ChromaDB → top-k → Claude Sonnet
2. **Vectorless RAG (PageIndex)** — VectifyAI's hosted PageIndex builds a hierarchical tree on their server; Claude Haiku reasons over it; Claude Sonnet generates the answer
3. **Self-Writing Wiki (Karpathy pattern)** — One Sonnet call ingests the PDF into a markdown wiki via the rulebook in `Knowledge-Base/CLAUDE.md`; queries read the wiki, never the raw source

Section 4 runs all three through 10 stress-test questions and scores them with Claude Opus as judge.

## Source document

`article.pdf` — Rahul Pandey's *"What does it take to be a data-driven organization?"* (DSciEr / Medium, 2023). Topics: data-first culture, lakehouse architecture, data + AI governance, data-as-product, data mesh, AI Center of Excellence, sustainable AI. Always work from this PDF. The 10 questions in cell 7 are calibrated to its content.

## Stack

- Python 3.12 (`.python-version`)
- **uv** for everything dependency-related (`pyproject.toml`, `uv.lock`). Never `pip install`; always `uv add` / `uv sync`.
- **Anthropic** via `ANTHROPIC_API_KEY` (in `.env`)
- **PageIndex hosted** via `PAGEINDEX_API_KEY` (in `.env`; free key from `dash.pageindex.ai/api-keys`)
- Models pinned in cell 5:
  - `claude-haiku-4-5-20251001` — cheap routing (tree-search, wiki-page picking)
  - `claude-sonnet-4-6` — answer generation, wiki-ingest
  - `claude-opus-4-7` — §4 judge
- ChromaDB `EphemeralClient` (in-RAM, fine for the demo)
- `sentence-transformers/all-MiniLM-L6-v2` for §1 embeddings (384-dim)

## Two CLAUDE.md files — DO NOT confuse them

| Path | Purpose |
|---|---|
| `./CLAUDE.md` (this file) | Project instructions for Claude Code working on this repo |
| `./Knowledge-Base/CLAUDE.md` | The wiki maintenance schema — used VERBATIM as the system prompt for §3.2's ingest call. Editing it changes how the wiki is built. |

Don't accidentally edit `Knowledge-Base/CLAUDE.md` thinking it's project instructions. It's a load-bearing prompt.

## File map

| Path | Purpose | Tracked? |
|---|---|---|
| `walkthrough.ipynb` | The main notebook (54 cells, §0–§4) | yes |
| `article.pdf` | Source document | yes |
| `pyproject.toml`, `uv.lock` | Dependency lockfile | yes |
| `.env`, `.env.example` | API keys (`.env` gitignored) | only `.example` |
| `Knowledge-Base/CLAUDE.md` | Wiki schema (system prompt for §3.2) | yes |
| `Knowledge-Base/README.md` | Vault README | yes |
| `Knowledge-Base/{Raw,Sources,Wiki,System,Archive}/.gitkeep` | Vault skeleton | yes |
| `Knowledge-Base/Raw/article.md` | Docling output written by §1.1 | no (regenerable, gitignored) |
| `Knowledge-Base/{Sources,Wiki,System,Archive}/*.md` | LLM-generated wiki contents | no (gitignored) |

## How to run

```bash
uv sync                                                          # install deps
uv run python -m ipykernel install --user --name rag2-0          # register kernel
uv run jupyter lab walkthrough.ipynb                            # open notebook
```

Run cells top to bottom. Heads-up:

- §1.1 first run downloads ~300 MB Docling model + ~90 MB sentence-transformers model. Subsequent runs are cached.
- §2.1 uploads the PDF to PageIndex; their server takes 30–90 s to build the tree.
- §3.2 ingest is a single 3–5 min Sonnet call (streams output via dots; system prompt is cached for reruns).
- §4 runs 30 (system × question) calls + 30 Opus judge calls. Budget ~5–8 min and well under $1.

## Editing conventions

- **Notebook cell edits**: mutate the `.ipynb` JSON via a Python script (`json.loads` / `json.dumps`). Don't use the `Edit` tool on the raw notebook file — too easy to break JSON quoting.
- **After every cell edit**, AST-parse all code cells:
  ```python
  import ast
  for c in nb['cells']:
      if c['cell_type'] == 'code':
          ast.parse(''.join(c['source']))
  ```
- **Cell narrative voice**: hacker-y direct ("No Noise. Just Build."). Existing emojis stay; don't add or strip without asking.
- **Function docstrings**: Karpathy-style — story-driven, why before what, tradeoffs called out, anticipate misunderstandings.
- **Commits**: don't auto-commit; user controls when.

## Don't

- Don't `pip install` — always `uv add` / `uv sync`.
- Don't switch §1's embedding model (`all-MiniLM-L6-v2`) without updating cell 15's narrative about 384-dim vectors.
- Don't hand-roll PageIndex — use the hosted `PageIndexClient`. The PyPI `pageindex` package IS the hosted client (its `api_key` arg goes to PageIndex, not OpenAI).
- Don't mix OpenAI into the per-query path — every per-query LLM call is Claude. PageIndex's server may use OpenAI internally for the one-time tree build; we don't control that and that's fine.
- Don't rename `article.pdf` or `Knowledge-Base/Raw/article.md` without updating cells 5, 11, 12, 35, 37 + `.gitignore`.
- Don't commit `.env`, `Knowledge-Base/Raw/article.md`, or anything under `Knowledge-Base/{Sources,Wiki,System,Archive}/*.md`.

## Comparison story for §4

Each question's `stresses` field predicts which architecture should win. The §4.5 "Read the failures" cell is the payoff — verifies whether the predictions held. Don't tune the questions to make any one system win; the value is in the failures.
