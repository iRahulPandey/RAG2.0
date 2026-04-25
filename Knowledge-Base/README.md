# Second Brain — AI/ML Research Wiki

A self-maintaining personal knowledge base following Karpathy's LLM Wiki pattern. Built on Obsidian + iCloud Drive + Cowork (Claude).

## The 30-second version

1. You dump things into `Raw/` from anywhere — Mac, iPhone, iPad, Obsidian Web Clipper.
2. You open Cowork and say **"ingest"**. Claude reads the new item, writes a source summary, creates/updates Wiki pages, and updates the Glossary/Index/Overview/Log.
3. You ask questions. Claude reads Wiki (not Raw) and answers with `[[wikilinks]]`. Good answers get saved as Analysis pages.
4. Occasionally you say **"lint"** and Claude finds orphans, contradictions, stale claims, and broken links.
5. Occasionally you say **"prune"** and Claude soft-archives stuff you don't need anymore.

Your job: find good sources, ask good questions, decide what matters.
Claude's job: everything else.

## How to actually use it today

### On Mac

1. Install Obsidian (`brew install --cask obsidian` or download from obsidian.md).
2. Open Obsidian → "Open folder as vault" → pick this folder (`~/iCloud Drive/Knowledge-Base`).
3. Hit `Cmd+G` for the graph view. It'll be empty; fill as you ingest.
4. Install the **Obsidian Web Clipper** browser extension → set default save location to `Raw/`.
5. Open Cowork with this folder mounted. Say hi, let it read `CLAUDE.md`, then start dumping into `Raw/`.

### On iPhone / iPad

1. Install Obsidian from the App Store.
2. When it asks where to store the vault, pick **"Store in iCloud"** and navigate to `iCloud Drive → Knowledge-Base`. (If iOS Obsidian doesn't find it, open the Files app, confirm `Knowledge-Base` is under iCloud Drive, then retry.)
3. Install Obsidian Web Clipper on mobile Safari (it's an iOS Safari extension) — clips go straight to `Raw/`.
4. For voice capture: use iOS Shortcuts or just dictate into a new note in Obsidian mobile → save to `Raw/`.

### On iCloud.com

Browser-only access via Files. Good for grabbing a note when you're on someone else's machine.

## Folder layout

```
Knowledge-Base/
├── CLAUDE.md         ← the schema (Claude reads this first every session)
├── README.md         ← this file
├── Raw/              ← you dump here. Immutable. Claude reads only.
├── Sources/          ← Claude writes a summary per Raw item
├── Wiki/             ← Concept / Product / Persona / Analysis pages
├── System/           ← Index, Glossary, Overview, Log
├── Archive/          ← soft-deleted pages (prune sends them here)
└── .obsidian/        ← Obsidian config; pre-tuned for this workflow
```

## Commands you'll say to Claude

- **`ingest`** — process new items in `Raw/`
- **`ingest <path>`** — process a specific raw item
- **`<question>?`** — ask anything; Claude reads Wiki and answers
- **`lint`** — health-check the wiki
- **`prune`** — propose soft-deletions
- **`what's new?`** — summary of recent log entries
- **`what do I think about X?`** — reads all pages touching X

## Rhythms

- **Daily (mobile, 5 min):** capture into Raw. Don't organize.
- **Weekly (Mac, 30 min):** open Cowork → "ingest everything new". Sit through each item, read the summary, steer.
- **Every ~10 ingests:** "lint".
- **Monthly (15 min):** "prune", then manually delete files in `Archive/` if you want.
- **Quarterly (1 hr):** re-read `System/Overview.md`. Ask Claude "what do I seem to believe now that I didn't three months ago?"

## Design doc

The full design doc and rationale lives at `~/…/AIOpt/karpathy-apple-notes-design.md` from this session. The Obsidian-specific version of the architecture is identical in structure (CLAUDE.md, three folders, four operations) but uses real wikilinks, real delete (via prune), and the Obsidian Web Clipper for capture.

## Credit

Pattern: Andrej Karpathy's `llm-wiki.md` idea document.
Walkthrough that sparked this setup: Balu Kosuri, *I used Karpathy's LLM Wiki to build a knowledge base that maintains itself with AI* (Medium, April 2026).
