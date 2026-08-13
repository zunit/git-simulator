# git-simulator

A fun simulator for people to practice their Git skills.

Interactive, browser-based training exercises that let people run Git commands against a
simulated repository — a real terminal prompt, a real commit graph, real merge conflicts —
without any risk to an actual project. Nothing is installed and nothing can be broken.

**Live site: https://zunit.github.io/git-simulator/**

## What's inside

Twelve self-contained simulators, presented through a hub page with a sidebar, a
dark/light theme toggle, and deep links to each exercise.

### 1 · Git Basics

| Simulator | Covers |
|---|---|
| Git Commit (Amend) | `git commit --amend`, rewriting messages, amending in Vim |
| Git Log | Reading history with `git log` |
| Git Ignore | `.gitignore` patterns and what they actually match |
| Git Stash | Shelving and restoring work in progress |
| Git Time Machine | Detached HEAD, branching from the past, `reset` vs `restore` vs `revert` |

### 2 · Git Intermediate

| Simulator | Covers |
|---|---|
| Git Branching | Creating and switching branches |
| Branch Merging | Fast-forward and three-way merges |
| Git Diff & Comparing Branches | `git diff` across commits and branches |
| Git Remotes & Push | Adding remotes and pushing |
| Git Fetch & Pull | `fetch` vs `pull`, tracking branches |
| Remote Merge Conflict | Resolving a conflict that arrives from a remote |
| GitLab Merge Request (MR/PR) | The review-and-merge workflow |

Each simulator runs entirely in the browser. React is bundled inside each file, so they
work offline; a few pull Google Fonts and will fall back to system fonts without a
connection.

## Repository layout

```
src/                 ← the deployed site. This is what GitHub Pages publishes.
  index.html           the hub page (sidebar, theme toggle, deep links)
  1_2 … 2_7 *.html     the twelve simulators, flat and numerically prefixed

1_raw_exercise/      ← authoring/archive area, organised by topic
  1_git_basics/        6 topics
  2_git_intermediate/  7 topics
                       mixed .html, .docx exercise sheets, .md instructor notes, one .mp4

.github/workflows/
  deploy-pages.yml     the deploy job
```

`src/` is a curated, renamed, deploy-ready copy of the exercises. **It has diverged from
`1_raw_exercise/`** — the dark-mode work was applied to `src/` only. Treat `src/` as the
source of truth for anything published, and `1_raw_exercise/` as the archive that also
holds the `.docx` handouts, instructor notes, and legacy simulator variants.

There is no build step, no package manager, and no test suite. Edits are content changes.

## Running locally

Open `src/index.html` in a browser and it works. But the hub loads each simulator into an
`<iframe>`, which some browsers block over `file://`, so prefer a local server:

```bash
cd src
python3 -m http.server 8777
```

Then visit http://localhost:8777/. Stop it with Ctrl-C.

## Deploying

The site deploys itself. There is no button to click and nothing to build.

### How it works

`.github/workflows/deploy-pages.yml` uploads `src/` to GitHub Pages whenever you push to
`main` **and** the push touches something under `src/`:

```yaml
on:
  push:
    branches: [main]
    paths: ['src/**']
  workflow_dispatch
```

### Steps

```bash
git switch main                    # the workflow only watches main
# ...make your edits under src/...
git add src/
git commit -m "Describe what changed and why"
git push
```

That's it. The push triggers the workflow; a minute or two later the change is live at
https://zunit.github.io/git-simulator/.

Confirm it's on its way:

```bash
git status -sb    # want: "## main...origin/main" with no [ahead N]
```

Then watch the run in the repo's **Actions** tab.

### One-time setup

Only needed once per repository (already done here): in **Settings → Pages**, set
**Source** to **GitHub Actions**. Leaving it on "Deploy from a branch" makes the workflow
succeed while the site never updates.

### Redeploying without a code change

If you change a Pages setting, or a run fails for an unrelated reason, trigger it by hand:
**Actions → Deploy GitHub Pages → Run workflow**. That's what `workflow_dispatch` is for.

### If it didn't deploy

Four things silently skip the deploy. In rough order of likelihood:

| Symptom | Cause | Fix |
|---|---|---|
| No run appears in Actions | Committed but never pushed | `git status -sb` shows `[ahead N]` → `git push` |
| No run appears in Actions | You're on another branch | `git switch main`, then merge or re-apply your work |
| Run is skipped | The push touched nothing in `src/` | Editing only `README.md`, `CLAUDE.md`, or the workflow will not deploy |
| Run is green, page looks old | Browser cache | Hard-refresh with Cmd/Ctrl + Shift + R |

The path filter is evaluated over the **whole push**, not per commit — so a push
containing several commits still deploys as long as any one of them touched `src/`.

## Editing the simulators

Two different file formats live in `src/`, and the difference matters before you edit.

**Plain HTML** — `1_3-git-log-simulation.html`. Readable markup with inline `<style>` and
`<script>`. Edit it directly.

**Bundled pages** — everything else. These have `<title>Bundled Page</title>` and a splash
screen. All markup, CSS, and app code live inside the `__bundler/template` payload:

- The template payload is a **JSON-escaped string on one physical line**, not base64. Hex
  colours and text appear verbatim, so careful find-and-replace on the raw file works, and
  the whole file stays a one-line diff.
- Do **not** decode and re-encode that payload. A `json.loads`/`json.dumps` round-trip is
  not byte-identical and rewrites ~150KB per file for no reason.
- The separate `__bundler/manifest` payload *is* gzip+base64, but it only contains React,
  ReactDOM, and the bundler runtime. It holds no page content — never edit it.

A caution learned the hard way: **do not run a blind global colour find-and-replace across
these files.** The same hex can be correct in one place and wrong in another — dark text on
a light button is fine, the identical dark text on the page background is invisible. Resolve
each occurrence against the background it actually sits on.

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 zunit.
