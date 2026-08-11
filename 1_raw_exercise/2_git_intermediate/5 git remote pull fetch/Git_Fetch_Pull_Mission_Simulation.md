# Git Remote Mission Simulation — Fetch & Pull

**Student Exercise**

## GIT REMOTE MISSION: INCOMING TRANSMISSIONS

### A Mini Simulation on Fetch & Pull

**Scenario:** Continue the mission to save the **Glitch Galaxy** game.

**Mission Objective:** Learn how changes made on the remote repository reach your computer, why `git fetch` and `git pull` are different, and what `origin/main` actually represents.

### Core commands

```bash
git fetch
git pull
```

### Supporting command

```bash
git status
```

**Recommended length:** 20–30 minutes  
**Placement:** Use this after **Mission 4 — Publish the Rescue Build** and before the feature-branch mission.

---

# Instructor Overview

## Teaching idea

Students should not memorize:

> "`fetch` gets stuff and `pull` also gets stuff."

Instead, the simulation gives each command a different problem to solve:

- **`git fetch`** — "Find out what changed remotely without changing my current branch."
- **`git pull`** — "Bring the remote update into my current branch."

The activity uses a visible local-versus-remote timeline so students can predict what each command will change **before** they run it.

---

## Learning outcomes

By the end of the mission, students should be able to:

- Explain the difference between:
  - the local branch `main`,
  - the remote-tracking branch `origin/main`,
  - the remote branch `main` on GitHub.
- Explain that `origin/main` is stored **locally** and represents Git's most recently fetched view of the remote `main` branch.
- Use `git fetch` to retrieve remote updates and refresh remote-tracking information.
- Recognize that `git fetch` does **not** integrate those updates into the current branch.
- Recognize that `git fetch` does **not** change the current working files.
- Use `git pull` to fetch remote updates and integrate the upstream branch into the current branch.
- Explain the practical difference between `git fetch` and `git pull`.
- Predict whether a Git command changes:
  - the local branch,
  - the remote-tracking branch,
  - the remote repository.

---

# Suggested Setup

Continue from the end of the existing **Publish the Rescue Build** mission.

Students should already have:

- cloned the repository,
- committed a local change,
- pushed it to GitHub,
- a local `main` branch that tracks `origin/main`.

Use a per-student repository, fork, or temporary classroom repository if possible.

At the beginning of this extension, the histories should match:

```text
YOUR COMPUTER                              GITHUB
────────────────────────────────────       ─────────────────

Local branch       Remote-tracking         Remote branch
main               origin/main             main

A -- B -- C        A -- B -- C             A -- B -- C
```

## Important mental model

There are **three things** to keep separate:

```text
LOCAL BRANCH        REMOTE-TRACKING BRANCH        REMOTE BRANCH
main                origin/main                   main on GitHub
```

`origin/main` is **not the live branch on GitHub**.

It is a remote-tracking reference stored in your local repository. It records where Git last observed the remote branch after communication such as a fetch.

A useful way to remember the three parts:

```text
main
"What I am currently working on."

origin/main
"What my local Git currently knows about origin's main."

GitHub main
"What is actually on the remote repository right now."
```

---

# Mission 5 — Intercept an Incoming Transmission

## Story

Your Shield Mode rescue build has been published.

While you continue working, **Mission Control sends a new emergency transmission directly to the GitHub repository**.

The remote repository has changed.

Your computer has not received the update yet.

### Your mission

Find out what changed at Mission Control **without changing your current branch or working files**.

---

## Stage 1 — Create the incoming transmission

For the simulation, use the GitHub web interface to imitate another teammate.

Open `transmission.txt` on GitHub and add:

```text
MISSION CONTROL UPDATE:
Boss Gate coordinates received.
```

Commit the change on GitHub with a message such as:

```text
Send boss gate coordinates
```

Then return to the terminal.

> **Simulation rule:** Do not clone the repository again.

Assume the new GitHub commit is **D**.

The situation is now:

```text
YOUR COMPUTER                              GITHUB
────────────────────────────────────       ─────────────────

Local branch       Remote-tracking         Remote branch
main               origin/main             main

A -- B -- C        A -- B -- C             A -- B -- C -- D
```

Notice something important:

**GitHub has commit D, but your local repository has not fetched it yet.**

---

## Prediction Checkpoint

Before typing the next Git command, answer:

1. Has your local `main` branch moved to commit D?
2. Has your local `origin/main` moved to commit D?
3. Has `transmission.txt` on your computer changed?
4. Which command could contact the remote and update what your local Git knows?

Do not continue until you have made a prediction.

---

## Stage 2 — Check your current files

Run:

```bash
cat transmission.txt
```

The new Boss Gate message should **not** appear yet.

Why?

The update happened on GitHub. Nothing has brought that update into your local repository yet.

---

## Stage 3 — Fetch the transmission

Run:

```bash
git fetch
```

You can also explicitly name the remote:

```bash
git fetch origin
```

For this exercise, either form communicates with the configured remote.

After the fetch, think of the repository like this:

```text
YOUR COMPUTER                              GITHUB
────────────────────────────────────       ─────────────────

Local branch       Remote-tracking         Remote branch
main               origin/main             main

A -- B -- C        A -- B -- C -- D        A -- B -- C -- D
```

### What changed?

`origin/main` moved forward.

### What did NOT change?

Your current `main` branch is still at C.

Your working files still match `main`.

---

## Evidence Check

Run:

```bash
git status
```

Because `main` tracks `origin/main`, Git should now be able to tell that your local branch is **behind** its upstream branch.

Now check the file again:

```bash
cat transmission.txt
```

The new Boss Gate message should still **not** appear in your working file.

That is the central idea of this mission.

---

## Mission 5 Checkpoint Questions

1. Did `git fetch` download information from the remote repository?
2. Did `origin/main` move?
3. Did your local `main` branch move?
4. Did your current working files change?
5. Where is commit D now?
6. Why might someone use `git fetch` before deciding whether to integrate remote work?

---

## Key Takeaway

> **`git fetch` retrieves remote updates and refreshes remote-tracking information without integrating those updates into your current branch.**

### Mental shortcut

**FETCH = "Show me what's new, but don't change my current work yet."**

---

# Mission 6 — Install the Mission Control Update

## Story

You have now learned that Mission Control has sent commit D.

The coordinates have been approved.

It is time to bring the incoming update into the version of Glitch Galaxy you are actually working on.

### Your mission

Update your current `main` branch with the upstream work.

---

## Starting State

After Mission 5:

```text
YOUR COMPUTER                              GITHUB
────────────────────────────────────       ─────────────────

Local branch       Remote-tracking         Remote branch
main               origin/main             main

A -- B -- C        A -- B -- C -- D        A -- B -- C -- D
```

Commit D has already been fetched.

But your current branch has not moved to it.

---

## Prediction Checkpoint

Before running the next command:

1. Which reference already knows about commit D: `main` or `origin/main`?
2. Will the next command change your current branch?
3. Should `transmission.txt` change after the update is integrated?

Make your prediction first.

---

## Pull the update

Run:

```bash
git pull
```

Conceptually, `git pull` performs two jobs:

```text
1. FETCH remote updates
2. INTEGRATE the appropriate upstream branch into your current branch
```

For this classroom mission, the histories were deliberately kept linear.

That means the integration should be a simple **fast-forward**:

```text
BEFORE PULL

main
A -- B -- C

origin/main
A -- B -- C -- D
```

After the pull:

```text
AFTER PULL

YOUR COMPUTER                              GITHUB
────────────────────────────────────       ─────────────────

Local branch       Remote-tracking         Remote branch
main               origin/main             main

A -- B -- C -- D   A -- B -- C -- D        A -- B -- C -- D
```

---

## Verify the installation

Run:

```bash
cat transmission.txt
```

You should now see:

```text
MISSION CONTROL UPDATE:
Boss Gate coordinates received.
```

Then run:

```bash
git status
```

Your local `main` should now be synchronized with its upstream branch for this exercise.

---

## Mission 6 Checkpoint Questions

1. Did `git pull` update the current branch?
2. Did the working file change after the pull?
3. Did `git pull` create commit D?
4. Where did commit D originally come from?
5. What is the biggest practical difference between `git fetch` and `git pull`?
6. Why can `git pull` work without typing `origin main` in this cloned repository?

---

## Key Takeaway

> **`git pull` fetches remote updates and then integrates the appropriate upstream branch into your current branch.**

### Mental shortcut

**PULL = "Get what's new and bring it into my branch."**

---

# Fetch vs. Pull — The Core Difference

| Command | What it does | Updates `origin/main`? | Updates current `main`? | Can working files change? |
|---|---|---:|---:|---:|
| `git fetch` | Retrieves remote updates and refreshes remote-tracking information | Yes | No | No |
| `git pull` | Fetches, then integrates the upstream branch into the current branch | Yes | Yes, when there is upstream work to integrate | Yes |

For this simulation:

```text
FETCH

GitHub main
A -- B -- C -- D
          |
          | git fetch
          v
origin/main
A -- B -- C -- D

main stays:
A -- B -- C
```

```text
PULL

GitHub / origin/main
A -- B -- C -- D
          |
          | git pull
          v
main
A -- B -- C -- D
```

---

# The Remote Workflow So Far

The complete mental model now becomes:

```text
git clone
Remote repository  ---------------------->  Local repository

git commit
Working changes    ---------------------->  Local Git history

git push
Local commits      ---------------------->  Remote repository

git fetch
Remote updates     ---------------------->  Local remote-tracking information

git pull
Remote updates     ---------------------->  Fetch + integrate into current branch
```

A useful three-direction memory aid:

```text
PUSH  = send my commits out
FETCH = learn what changed remotely
PULL  = bring remote work into my branch
```

---

# Simulation Debrief

Complete these without looking back at the commands.

### 1. A teammate pushed a new commit to GitHub.

You want your local Git repository to learn about the change, but you do **not** want your current branch or working files changed yet.

Command:

```text
____________________________________
```

### 2. After fetching, which one can move even though your current branch does not?

```text
main / origin/main
```

Answer:

```text
____________________________________
```

### 3. Is `origin/main` the actual live branch stored on GitHub?

```text
____________________________________
```

Explain:

```text
____________________________________
```

### 4. You want to retrieve the upstream changes and integrate them into your current branch.

Command:

```text
____________________________________
```

### 5. Complete the mental models.

```text
FETCH = _______________________________________________

PULL  = _______________________________________________
```

### 6. A remote commit exists on GitHub.

After `git fetch`, the new commit is represented by `origin/main`, but your working files have not changed.

Is this expected?

```text
____________________________________
```

Explain:

```text
____________________________________
```

---

# Student Cheat Sheet — Incoming Remote Changes

| Command | When you use it | Mental model |
|---|---|---|
| `git fetch` | You want to retrieve remote updates without integrating them into your current branch | **Check/download what's new** |
| `git fetch origin` | Same idea, but explicitly names the remote | **Fetch from origin** |
| `git pull` | You want to fetch and integrate the upstream changes into your current branch | **Get + integrate** |
| `git status` | You want to compare the current branch with its configured upstream after Git has current remote-tracking information | **Where does my branch stand?** |

## Three names to remember

| Name | Where it exists | Meaning |
|---|---|---|
| `main` | Your computer | Your local working branch |
| `origin/main` | Your computer | Your local remote-tracking reference for `origin`'s `main` |
| `main` on GitHub | Remote repository | The actual remote branch |

### One sentence to remember

> **Fetch updates what your local Git knows about the remote; pull goes further and integrates upstream work into your current branch.**

---

# Optional Pair Challenge — Live Mission Control

If students are working in pairs, make the incoming update real.

## Student A — Mission Control

1. Make a small change.
2. Commit it.
3. Push it.

Example:

```bash
git add transmission.txt
git commit -m "Send emergency coordinates"
git push
```

## Student B — Rescue Pilot

Before fetching:

1. Predict whether your files have changed.
2. Run `git fetch`.
3. Run `git status`.
4. Confirm your files still have not changed.
5. Run `git pull`.
6. Confirm the incoming change now appears.

Then switch roles.

### Challenge question

At which exact step did Student B's working file change?

```text
____________________________________
```

---

# Instructor Notes & Answer Key

## Debrief Answers

1. `git fetch`
2. `origin/main`
3. No. `origin/main` is a remote-tracking reference stored in the local repository. It represents Git's locally recorded view of the remote branch.
4. `git pull`
5. Suggested answers:
   - **FETCH = retrieve/learn about remote updates without integrating them into the current branch**
   - **PULL = fetch remote updates and integrate the upstream branch into the current branch**
6. Yes. That is the intended behavior of `git fetch`.

---

## Accuracy Notes for the Instructor

### 1. `origin/main` is local

This is the most important conceptual correction to make before teaching fetch.

Avoid diagrams that place `origin/main` inside the GitHub box.

Use:

```text
YOUR COMPUTER                      GITHUB

main
origin/main                        main
```

`origin/main` is a local remote-tracking reference.

---

### 2. Fetch does not mean "change my files"

`git fetch` retrieves remote data and updates the relevant remote-tracking references.

It does not integrate the fetched branch into the current branch.

For this mission:

```text
main         stays at C
origin/main  moves to D
```

The working files therefore continue to reflect local `main` at C.

---

### 3. Teach pull as "fetch + integrate"

For modern Git, the safest beginner description is:

> **`git pull` fetches and then integrates the appropriate upstream branch into the current branch.**

Avoid presenting this as the universal equation:

```text
git pull = git fetch + git merge
```

because Git can use different integration strategies depending on command options and configuration.

For this classroom simulation, the history is deliberately linear, so the pull should simply fast-forward `main`.

---

### 4. Keep the exercise conflict-free

Before the remote GitHub edit:

```text
Local main:    A -- B -- C
Remote main:   A -- B -- C
```

Only the remote should then advance:

```text
Local main:    A -- B -- C
Remote main:   A -- B -- C -- D
```

Do not let students create a different local commit before the pull mission.

That would create divergent history and could introduce reconciliation choices or conflicts that distract from the learning goal.

---

### 5. IDE auto-fetch can change the observation

Some Git GUIs and IDEs automatically fetch in the background.

If that happens, `origin/main` may already be updated before the student manually runs `git fetch`.

For the cleanest classroom demonstration:

- use the terminal,
- avoid automatic background fetch during this mission,
- or explain that an IDE may already have performed the fetch operation for them.

---

### 6. Why `git pull` can be short

Because the repository was cloned and `main` normally has an upstream tracking relationship, Git already knows which remote branch the local branch follows.

That is why students can normally use:

```bash
git pull
```

instead of specifying the remote and branch every time.

This also reinforces the earlier lesson about upstream tracking.

---

# Recommended Placement in the Existing Simulation

Use the following order:

1. **Mission 1 — Recover the Lost Game** → `git clone`
2. **Mission 2 — Locate Mission Control** → `git remote -v`
3. **Mission 3 — Build the Secret Power-Up** → local commit
4. **Mission 4 — Publish the Rescue Build** → `git push`
5. **Mission 5 — Intercept an Incoming Transmission** → `git fetch`
6. **Mission 6 — Install the Mission Control Update** → `git pull`
7. **Mission 7 — Create a Hero Upgrade Branch** → `git push -u origin <branch>`
8. **Bonus Mission — The Project Started Locally** → `git remote add origin <url>`

This creates a natural progression:

```text
CLONE
  ↓
INSPECT REMOTE
  ↓
COMMIT LOCALLY
  ↓
PUSH OUT
  ↓
FETCH IN
  ↓
PULL + INTEGRATE
  ↓
CREATE + TRACK A NEW BRANCH
```

The story now covers both directions of everyday remote Git work:

```text
YOUR COMPUTER  <---------->  REMOTE REPOSITORY
                 fetch/pull
              push -------->
```
