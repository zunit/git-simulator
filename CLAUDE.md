# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**git-simulator** is a Git-skills training project containing interactive exercises and simulators to teach Git concepts. It is a **purely static/document repository** with no build system, no code compilation, no tests to run, and no lint/format tooling.

The project is MIT-licensed (2026 zunit) and contains:
- Interactive HTML simulators (self-contained training simulations)
- .docx exercise instructions and learning guides
- .md instructor-facing "mission" documents with teaching guidance
- One .mp4 video walkthrough

There are **no package.json, Makefile, Python requirements, npm/pip commands, or CI/CD pipelines** — edits are purely content updates.

## Content Structure

All training content lives under `1_raw_exercise/`, split into two difficulty levels:

```
1_raw_exercise/
├── 1_git_basics/                       6 foundational topics
│   ├── 1 git add/
│   ├── 2 git commit/                   (includes amend and interactive rebase)
│   ├── 3 git log/
│   ├── 4 gitignore/
│   ├── 5 git stash/
│   └── 6 git general timeline/
│
└── 2_git_intermediate/                 7 intermediate topics
    ├── 1 branching basics/
    ├── 2 branch merging basics/
    ├── 3 git diff and comparing branches/
    ├── 4 git remotes push/
    ├── 5 git remote pull fetch/
    ├── 6 git remote merge conflict/    (note: folder name has trailing space)
    └── 7 git MR - PR/
```

Each topic folder contains a **mix of file types**:
- **HTML** — interactive simulators (self-contained, opened in browser)
- **.docx** — exercise instructions, student materials, key learning points
- **.md** — instructor-facing "mission" documents (narrative + teaching guide; see _Mission Doc Pattern_ below)
- **.mp4** — one walkthrough video (in `1_git_basics/4 gitignore/`)

## HTML File Formats

Two distinct HTML authoring styles coexist in this repo:

### Plain Single-File Simulators
**Examples:** `git-log-simulation.html`, all `Legacy*` prefixed files
- Hand-written, readable HTML with inline `<script>` and `<style>`
- No external CDN dependencies
- Edit directly as plain text
- Typically 150–400 lines

### "Bundled Page" React Export Format
**Examples:** `Git Ignore Simulator.html`, `Git Branching Simulator (Wiz Staff).html`, all `*Mission.html` files
- Exported/bundled files with a placeholder `<title>Bundled Page</title>`
- Contains a splash screen and a base64-encoded payload
- Payload unpacks at runtime and pulls React 18 + Google Fonts from CDN
- **Cannot be hand-edited** — the readable markup is encoded inside the payload
- To edit these, you must decode/extract the embedded content (or regenerate from source)
- Typically 200–400 lines

When you encounter a new HTML file:
1. **Check the `<title>` tag:** if it says `Bundled Page`, the markup is encoded — don't try to edit it as plain HTML.
2. **Check for an inline `<script>` with base64 strings and "unpkg.com" references:** another sign it's a bundled export.

## Mission Document Pattern

The `.md` files (`Git_Fetch_Pull_Mission_Simulation.md`, `Git_Multi_Remote_Merge_Conflict_Mission_Simulation.md`) follow a consistent template pairing a **themed sci-fi narrative** with an **Instructor Overview**:

- **Narrative section** — student-facing story framing (e.g., "INCOMING TRANSMISSIONS", "COLLISION COURSE") that gamifies the Git exercise
- **Instructor Overview** — structured teaching guide covering:
  - Core Git commands covered
  - Recommended session length and curriculum placement
  - Teaching idea/rationale
  - Explicit learning outcomes (bullet list)
  - Prerequisites and suggested setup steps

When creating new mission documents, follow this two-part structure. Pair each new `.md` mission with a corresponding HTML simulator (either hand-written or bundled).

## Known Naming Quirks

Be aware of these inconsistencies if you're modifying or organizing content:

- **Trailing space in folder name:** `2_git_intermediate/6 git remote merge conflict ` has a trailing whitespace character — some scripts or tools may trip on this when referencing the folder.

- **"Legacy" duplicate simulators:** Two topics have both a "Legacy" version and a current version with no clear authority marker:
  - `2 git commit/`: three legacy files (`Legacy git-commit-*.html`) + one current (`Git Amend Simulator Overview.html`)
  - `4 gitignore/`: one legacy file (`Legacy gitignore-simulation.html`) + one current (`Git Ignore Simulator.html`)
  - Do not delete a "Legacy" file without confirming it's superseded.

- **Misspelling in legacy filename:** `Legacy git-commit-ammend-message-simulation.html` misspells "amend" as "ammend" — this is the actual filename, not a typo to fix.

- **Audience-gated variants:** `1 branching basics/` has two nearly-identical files:
  - `Git Branching Simulator (Wiz Staff).html`
  - `Git Branching Simulator (Non Wiz Staff).html`
  - These are intentional variants for different audiences; do not consolidate without confirming.

- **Naming convention split:** Three file-naming styles coexist:
  - Title Case with spaces: `Git Ignore Simulator.html`
  - kebab-case: `git-log-simulation.html`
  - Snake_Case: `Git_Fetch_Pull_Mission_Simulation.md`
  - No single rule governs naming — match the existing file's style when adding content to the same topic folder.

- **"Mission" vs "Simulator" naming:** Intermediate topics 4–7 use "...Mission.html" (narrative/gamified), while basics and intermediate topics 1–3 use "...Simulator.html" — this reflects an evolving design convention over time, not a categorization rule.

## What to Expect When Editing

- **No commands to run** — no `npm install`, `make build`, `pytest`, etc.
- **HTML edits:** Check the file format first (plain vs bundled) before attempting to edit.
- **Docx files:** Not directly editable as text; edit in Word/Google Docs or convert to another format if needed.
- **Markdown edits:** Treat as plain text; follow the Mission pattern for new mission docs.
- **Git commits:** Edits to these training materials are good candidates for clear commit messages describing what exercise/topic was updated and why.
