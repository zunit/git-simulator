# Git Remote Mission Simulation — Multi-Remote Merges & Conflict Rescue

**Student Exercise — Continuation Mission**

## GIT REMOTE MISSION: COLLISION COURSE

### A Mini Simulation on Multiple Remotes, `git fetch --all`, Remote-Tracking Merges & Merge Conflicts

**Scenario:** The Glitch Galaxy rescue operation has expanded. Your project now receives updates from more than one remote repository, and two teams are beginning to modify the same code.

**Mission Objective:** Learn how to work with multiple remotes, fetch updates from all configured remotes, manually merge a remote-tracking branch, recognize when Git can merge automatically, and safely resolve a real merge conflict.

### Core commands

```bash
git remote add upstream <repository-url>
git remote -v
git fetch --all
git branch -r
git merge <remote>/<branch>
git status
git add <file>
git commit
git push
```

### Recovery command

```bash
git merge --abort
```

### Optional visualization command

```bash
git log --oneline --graph --decorate --all
```

**Recommended length:** 35–50 minutes

---

# Prerequisite

This mission assumes you have already completed the earlier Glitch Galaxy remote missions and understand:

- `git clone`
- `git remote -v`
- `git push`
- `git push -u`
- `git fetch`
- `git pull`
- the difference between `main`, `origin/main`, and the actual remote branch on GitHub.

You should already know this mental model:

```text
YOUR COMPUTER                         REMOTE REPOSITORY

Local branch      Remote-tracking     Remote branch
main              origin/main         main
```

This mission continues from there.

---

# Instructor Overview

## Teaching idea

Students already know how to push changes out and pull changes in.

The next challenge is understanding what happens when:

1. there is **more than one remote repository**,
2. `git fetch --all` updates information from all of them,
3. students manually choose which fetched branch to merge,
4. two development histories diverge,
5. Git can merge some divergence automatically,
6. but other divergence creates a **merge conflict** that requires a human decision.

The activity intentionally shows a **clean merge before a conflicting merge**.

That distinction matters:

> Different histories do not automatically mean a conflict.

A conflict occurs when Git cannot confidently combine the changes automatically.

---

# Learning Outcomes

By the end of this mission, students should be able to:

- Explain that one local Git repository can have multiple configured remotes.
- Explain that remote names such as `origin` and `upstream` are local nicknames.
- Add a second remote with `git remote add`.
- Inspect configured remotes with `git remote -v`.
- Explain what `git fetch --all` means.
- Recognize that `git fetch --all` fetches configured **remotes**; it does not create a local branch for every remote branch.
- Inspect known remote-tracking branches with `git branch -r`.
- Explain that `upstream/main` or `origin/main` are local remote-tracking references.
- Manually merge a fetched remote-tracking branch into the current branch.
- Explain the direction of `git merge <branch>`.
- Recognize the difference between:
  - a fast-forward,
  - a clean true merge,
  - a merge conflict.
- Use `git status` during a conflict.
- Read the basic conflict markers:
  - `<<<<<<<`
  - `=======`
  - `>>>>>>>`
- Resolve a conflict by editing the file, staging the resolved file, and completing the merge commit.
- Use `git merge --abort` to abandon a merge in progress when appropriate.
- Explain that resolving a merge locally does not publish the result until `git push`.

---

# Instructor Setup

For the clearest classroom version, use a fork-style setup:

```text
STUDENT REMOTE                         MISSION CONTROL REMOTE
origin                                 upstream

student/glitch-galaxy                  course/glitch-galaxy
```

The repositories must share common Git history.

A typical setup is:

- `upstream` = the instructor or course repository
- `origin` = the student's fork or personal repository

Students should already have `origin` because they cloned their personal copy.

They will add `upstream` during the mission.

---

# The New Mental Model — More Than One Remote

Until now, students may have pictured only one remote:

```text
YOUR COMPUTER
main
origin/main

      |
      v

origin
```

Now the same local repository can track information from multiple remotes:

```text
                         YOUR COMPUTER

Local branch             Remote-tracking references

main                     origin/main
                         upstream/main
                         upstream/sensor-patch


          origin                         upstream
     Student repository              Mission Control
```

Important:

```text
origin/main
upstream/main
upstream/sensor-patch
```

are all **local remote-tracking references**.

They are not the live branches sitting on the servers.

---

# Mission 7 — Open a Second Communication Channel

## Story

Until now, your Glitch Galaxy rescue ship has communicated only with your personal repository, called `origin`.

Mission Control has now opened a second official repository containing emergency patches.

Your computer needs a nickname for this second remote.

The team chooses:

```text
upstream
```

### Your mission

Connect the existing local repository to Mission Control without replacing `origin`.

---

## Stage 1 — Inspect the current channels

Run:

```bash
git remote -v
```

You should see your existing remote, usually something similar to:

```text
origin  <your-repository-url> (fetch)
origin  <your-repository-url> (push)
```

### Prediction Checkpoint

Before adding anything:

1. Is `origin` a special word meaning GitHub?
2. Can one repository have more than one remote?
3. Will adding another remote delete `origin`?

Make a prediction.

---

## Stage 2 — Add Mission Control

Run:

```bash
git remote add upstream <mission-control-repository-url>
```

Now inspect again:

```bash
git remote -v
```

You should see two remote names:

```text
origin    <student-repository-url>          (fetch)
origin    <student-repository-url>          (push)
upstream  <mission-control-repository-url>  (fetch)
upstream  <mission-control-repository-url>  (push)
```

You may not have permission to push to the instructor repository.

That is okay.

A remote can still be useful as a source from which you fetch updates.

---

## Prediction Checkpoint

Immediately after:

```bash
git remote add upstream <url>
```

has Git necessarily downloaded all of the branches from `upstream` yet?

```text
YES / NO
```

Explain:

```text
________________________________________________________
```

---

## Key Takeaway

> **A Git repository can have multiple remotes. Remote names such as `origin` and `upstream` are local nicknames for repository locations.**

### Mental shortcut

```text
origin   = my publishing remote
upstream = Mission Control's repository
```

That naming convention is common, but the names themselves are configurable.

---

# Mission 8 — Scan Every Relay

## Story

Mission Control reports that several stations may have new data.

You could contact each remote one at a time:

```bash
git fetch origin
git fetch upstream
```

But headquarters wants one command that checks every configured remote.

### Your mission

Fetch updates from all configured remotes without merging anything into `main`.

---

## Before the Scan

Assume the remotes currently contain:

```text
origin
└── main

upstream
├── main
└── sensor-patch
```

Mission Control has created the new branch:

```text
sensor-patch
```

Your local repository may not know about it yet.

---

## Prediction Checkpoint — What Does `--all` Mean?

Consider:

```bash
git fetch --all
```

Which interpretation is correct?

### A

```text
Create a local branch for every branch that exists anywhere.
```

### B

```text
Fetch from all configured remotes and update the appropriate
remote-tracking information.
```

### C

```text
Merge every remote branch into main.
```

Make your prediction before continuing.

---

## Stage 1 — Fetch everything from the configured remotes

Run:

```bash
git fetch --all
```

Git contacts the configured remotes and updates the remote-tracking references that their fetch configuration says to track.

For this classroom setup, think:

```text
origin
    ↓ fetch
origin/main

upstream
    ↓ fetch
upstream/main
upstream/sensor-patch
```

---

## Stage 2 — Inspect what your local Git now knows

Run:

```bash
git branch -r
```

You may see something similar to:

```text
origin/main
upstream/main
upstream/sensor-patch
```

These are **remote-tracking branches known to your local repository**.

Now run:

```bash
git branch
```

You may still see only:

```text
* main
```

---

## Critical Observation

`git fetch --all` did **not** automatically create:

```text
sensor-patch
```

as a local working branch.

Instead, your local Git learned about:

```text
upstream/sensor-patch
```

---

## Check Your Working Files

Run:

```bash
git status
```

Your current branch should still be your current branch.

`git fetch --all` has not merged all the remote work into it.

---

## Mission 8 Checkpoint Questions

1. How many remotes did `git fetch --all` contact in this exercise?
2. Did it merge `upstream/sensor-patch` into `main`?
3. Did it create a local `sensor-patch` branch automatically?
4. What does `git branch -r` show?
5. What is the difference between:
   - `sensor-patch`
   - `upstream/sensor-patch`?

---

## Key Takeaway

> **`git fetch --all` means fetch from all configured remotes. It does not mean "merge everything," and it does not turn every remote branch into a local branch.**

### Mental shortcut

```text
git fetch origin
= scan one remote

git fetch --all
= scan all configured remotes
```

---

# Mission 9 — Manually Install a Remote Patch

## Story

Mission Control's `sensor-patch` has passed testing.

Your team does not want to run `git pull` blindly.

Instead, you will perform the steps manually:

```text
FETCH
then
MERGE a specific fetched branch
```

This lets you see exactly what is being integrated.

---

# Part A — Create Independent Local Work

Before merging the Mission Control patch, make one harmless local commit.

Create:

```text
pilot-notes.txt
```

with:

```text
Pilot report:
Navigation systems stable.
```

Then commit it:

```bash
git add pilot-notes.txt
git commit -m "Add pilot navigation report"
```

Now your local history has moved forward independently.

Conceptually:

```text
                 L
                /
A -- B -- C -- D
                \
                 H
```

Where:

```text
L = your local pilot-notes commit
H = Mission Control's sensor-patch commit
```

The two commits share history, but neither contains the other.

---

# Part B — Fetch Before Merging

Make sure your remote-tracking information is current:

```bash
git fetch --all
```

Inspect:

```bash
git branch -r
```

Confirm that you can see:

```text
upstream/sensor-patch
```

---

## Prediction Checkpoint — Merge Direction

You are currently on:

```text
main
```

You run:

```bash
git merge upstream/sensor-patch
```

Which statement is correct?

### A

`main` is merged into `upstream/sensor-patch`.

### B

`upstream/sensor-patch` is merged into your current `main` branch.

Make a prediction.

---

# Part C — Perform the Merge

First confirm you are on `main`:

```bash
git status
```

Then run:

```bash
git merge upstream/sensor-patch -m "Merge Mission Control sensor patch"
```

Because your local commit and the remote patch changed different content, Git should be able to combine them automatically.

Conceptually:

```text
BEFORE MERGE

          L  main
         /
A-B-C-D
         \
          H  upstream/sensor-patch
```

After a successful non-fast-forward merge:

```text
          L-------M  main
         /       /
A-B-C-D
         \     /
          H---     upstream/sensor-patch
```

`M` records the joined histories.

---

# Very Important: Did Merge Contact the Server?

No new network communication is required just because you typed:

```bash
git merge upstream/sensor-patch
```

You had already fetched the commits.

The name:

```text
upstream/sensor-patch
```

refers to the locally stored remote-tracking reference.

The merge uses the fetched Git history already available in your local repository.

---

## Visualize the Result

Optional but highly recommended:

```bash
git log --oneline --graph --decorate --all
```

Look for the two histories joining.

---

## Push the Combined Result

The merge happened locally.

It is not automatically published to your `origin`.

Run:

```bash
git push
```

Now your normal publishing remote can receive the merged history.

---

## Mission 9 Checkpoint Questions

1. What branch were you on when you ran `git merge`?
2. Which history was merged into it?
3. Did `git merge upstream/sensor-patch` automatically contact Mission Control?
4. Why did this merge succeed without a conflict?
5. Where was the merge result created first: locally or remotely?
6. Which command published the result afterward?

---

## Key Takeaway

> **`git merge <branch>` incorporates the named branch or commit into the branch you currently have checked out.**

And:

> **A remote-tracking branch such as `upstream/sensor-patch` can be merged because its fetched commits already exist locally.**

### Mental shortcut

```text
I am standing on main.

git merge X

means:

Bring X into where I am standing.
```

---

# Mission 10 — Collision Course

## Story

The reactor needs urgent tuning.

Unfortunately, you and Mission Control both change the **same reactor setting** before either side sees the other's decision.

This time, Git cannot safely decide which value is correct.

Welcome to a merge conflict.

---

# Stage 1 — Establish the Shared Reactor Setting

Create:

```text
reactor.txt
```

with:

```text
Reactor Output: 50
```

Commit it:

```bash
git add reactor.txt
git commit -m "Add reactor control setting"
```

Publish it:

```bash
git push
```

At this point, local `main` and `origin/main` should share the same reactor line.

Conceptually:

```text
A -- ... -- M -- R
                  ^
                  shared starting point
```

Where commit `R` contains:

```text
Reactor Output: 50
```

---

# Stage 2 — Make a Local Reactor Decision

On your computer, change:

```text
Reactor Output: 50
```

to:

```text
Reactor Output: 75
```

Commit the local decision:

```bash
git add reactor.txt
git commit -m "Increase reactor output locally"
```

Do **not** push this commit.

Your local history now contains a change the remote does not know about.

---

# Stage 3 — Mission Control Makes a Different Decision

Open the same `reactor.txt` file in your `origin` repository using the GitHub web interface.

The remote should still show:

```text
Reactor Output: 50
```

Change it remotely to:

```text
Reactor Output: 90
```

Commit the GitHub change with a message such as:

```text
Increase reactor output from Mission Control
```

Now the histories have diverged:

```text
                    LOCAL
                      L
                     /
A -- ... -- M -- R
                     \
                      H
                    REMOTE
```

Where:

```text
L contains Reactor Output: 75
H contains Reactor Output: 90
```

Both changed the same line from the same base value.

---

# Prediction Checkpoint

Before fetching:

1. Does your local `origin/main` necessarily know about the new remote commit yet?
2. Will `git fetch origin` change your local `main`?
3. After fetching, will `main` and `origin/main` point to the same commit?
4. What might happen when you try to merge them?

---

# Stage 4 — Fetch the Remote Decision

Run:

```bash
git fetch origin
```

Now think of the histories like this:

```text
                      L  main
                     /
A -- ... -- M -- R
                     \
                      H  origin/main
```

The remote change is now represented locally by:

```text
origin/main
```

But your current `main` still contains your local decision.

---

# Stage 5 — Attempt the Merge

Before merging, make sure your working tree is clean:

```bash
git status
```

Then run:

```bash
git merge origin/main
```

Git should report a conflict in:

```text
reactor.txt
```

The merge stops so that **you** can decide what the final content should be.

---

# What Just Happened?

Git could see:

### Common ancestor

```text
Reactor Output: 50
```

### Your current branch

```text
Reactor Output: 75
```

### Incoming history

```text
Reactor Output: 90
```

Git knows both sides intentionally changed the same area.

It does not randomly choose one.

---

# Stage 6 — Ask Git What Is Wrong

Run:

```bash
git status
```

Git should report an unmerged path or conflict involving:

```text
reactor.txt
```

This is an important habit:

> **When Git stops during a merge, run `git status`.**

It tells you what state the repository is in and which files still need attention.

---

# Stage 7 — Inspect the Conflict Markers

Open:

```text
reactor.txt
```

You should see something similar to:

```text
<<<<<<< HEAD
Reactor Output: 75
=======
Reactor Output: 90
>>>>>>> origin/main
```

## How to Read This

### `<<<<<<< HEAD`

Begins the version from your current checked-out branch.

For this mission:

```text
Reactor Output: 75
```

### `=======`

Separates the two conflicting versions.

### `>>>>>>> origin/main`

Ends the incoming version being merged.

For this mission:

```text
Reactor Output: 90
```

---

# Important Misconception Check

Resolving a conflict does **not** mean:

```text
Delete the strange symbols and keep whatever text happens to remain.
```

Resolving means:

> **Decide what the final correct file should contain.**

You may choose:

```text
Reactor Output: 75
```

or:

```text
Reactor Output: 90
```

or, if the team decides on a compromise:

```text
Reactor Output: 85
```

The correct answer is determined by the intended program behavior, not by Git.

Git identifies the disagreement.

Humans decide the correct resolution.

---

# Stage 8 — Resolve the Conflict

For this simulation, Mission Control authorizes:

```text
Reactor Output: 85
```

Edit `reactor.txt` so the entire file contains:

```text
Reactor Output: 85
```

Make sure all conflict markers are removed.

Then tell Git that this file is resolved:

```bash
git add reactor.txt
```

---

# Stage 9 — Check Before Finishing

Run:

```bash
git status
```

Git should now recognize that the conflict in `reactor.txt` has been resolved and staged.

The merge itself still needs to be completed.

---

# Stage 10 — Complete the Merge

Run:

```bash
git commit -m "Resolve reactor output conflict"
```

You have now created the merge result locally.

Optional alternative after staging all conflicts:

```bash
git merge --continue
```

For this exercise, the explicit `git commit` command makes the final step easier to see.

---

# Stage 11 — Publish the Resolution

The conflict resolution exists only in your local history until you publish it.

Run:

```bash
git push
```

Now the shared remote contains the resolved history.

---

# Final Conflict Diagram

Before the merge:

```text
              L  main
             /
A -- ... -- R
             \
              H  origin/main
```

After resolution:

```text
              L------C  main
             /      /
A -- ... -- R
             \    /
              H--
```

Where:

```text
C = merge commit containing the resolved Reactor Output
```

After:

```bash
git push
```

the remote can also point to the resolved commit.

---

# Emergency Escape Hatch — Abort the Merge

Sometimes you begin a merge and realize:

- you merged the wrong branch,
- you need more information,
- you are not ready to resolve the conflict.

If the merge is still in progress, you can use:

```bash
git merge --abort
```

This tells Git to abandon the merge attempt and try to return to the pre-merge state.

### Important habit

Start merges with a clean working tree whenever possible.

Do not use `git merge --abort` as a substitute for understanding what happened.

Use it as a controlled way to back out of a merge attempt.

---

# Mission 10 Checkpoint Questions

1. Why did `git fetch origin` not resolve the disagreement?
2. Why did `git merge origin/main` stop?
3. What does `HEAD` represent in the conflict marker shown in this mission?
4. What does the text below `=======` represent?
5. Who decides the correct final value: Git or the developer?
6. What does `git add reactor.txt` mean during conflict resolution?
7. Which command completed the merge?
8. Which command published the resolution?
9. When would `git merge --abort` be useful?

---

# Clean Merge vs. Merge Conflict

A critical lesson from Missions 9 and 10:

## Divergence with different changes

```text
LOCAL:
pilot-notes.txt changed

REMOTE:
sensor.txt changed
```

Git can often combine these automatically.

Result:

```text
CLEAN MERGE
```

## Divergence with competing changes

```text
LOCAL:
Reactor Output: 75

REMOTE:
Reactor Output: 90
```

Both changed the same area differently.

Result:

```text
MERGE CONFLICT
```

### One sentence to remember

> **A merge conflict is not "two branches exist." It is a situation where Git cannot automatically determine the correct combined result.**

---

# Fetch, Merge and Pull — Connecting the Concepts

In the previous mission you used:

```bash
git pull
```

and learned:

```text
fetch
+
integrate
```

Now you have seen the process more explicitly.

A merge-based workflow can be performed manually as:

```bash
git fetch origin
git merge origin/main
```

This gives you a deliberate pause between:

```text
LEARN WHAT CHANGED
```

and:

```text
INTEGRATE IT
```

That pause can be useful when you want to inspect remote changes before modifying your current branch.

Do not memorize `git pull` as universally equal to exactly two fixed commands under every possible Git configuration.

The important model is:

```text
git fetch
= update remote information

git merge
= integrate a selected history into my current branch

git pull
= fetch, then integrate the selected/upstream history
```

---

# `git fetch --all` — What It Does and Does Not Mean

## It DOES

```text
Contact all configured remotes
and fetch their configured updates.
```

For example:

```text
origin
upstream
```

can both be fetched.

## It DOES NOT mean

```text
Create every remote branch as a local branch.
```

## It DOES NOT mean

```text
Merge every branch into main.
```

## It DOES NOT mean

```text
Pull every branch.
```

### Memory aid

```text
--all refers to REMOTES being fetched,
not "merge all branches."
```

---

# Remote Names vs. Remote-Tracking Branches

These are different:

## Remote name

```text
origin
upstream
```

A nickname for a repository location.

## Remote-tracking branch

```text
origin/main
upstream/main
upstream/sensor-patch
```

A local reference representing fetched knowledge about a branch from that remote.

---

# Simulation Debrief

Complete these without looking back.

## 1. Your repository has two remotes, `origin` and `upstream`.

You want to fetch configured updates from both.

Command:

```text
________________________________________
```

---

## 2. Does `git fetch --all` create a local working branch for every remote branch?

```text
________________________________________
```

Explain:

```text
________________________________________
```

---

## 3. What command lists the remote-tracking branches currently known locally?

```text
________________________________________
```

---

## 4. You are currently on `main`.

What does this mean?

```bash
git merge upstream/sensor-patch
```

Answer:

```text
________________________________________
```

---

## 5. Does `git merge upstream/sensor-patch` itself need to download that branch from the server if you already fetched it?

```text
________________________________________
```

---

## 6. Two branches changed different files.

Must there be a merge conflict?

```text
________________________________________
```

---

## 7. Two branches changed the same line differently.

Git stops and reports a conflict.

What command should you run first to inspect repository state?

```text
________________________________________
```

---

## 8. Complete the conflict-resolution workflow.

```text
1. Inspect the conflicted file
2. ______________________________________
3. git add <resolved-file>
4. ______________________________________
5. git push
```

---

## 9. What does this do?

```bash
git merge --abort
```

Answer:

```text
________________________________________
```

---

## 10. Which happens first?

```text
A. Push the merge result
B. Resolve and commit the merge locally
```

Answer:

```text
________________________________________
```

---

# Student Cheat Sheet — Remote Merges & Conflicts

| Command | When you use it | Mental model |
|---|---|---|
| `git remote -v` | Inspect configured remotes and URLs | **Which servers do I know?** |
| `git remote add upstream <url>` | Add another remote repository | **Add another communication channel** |
| `git fetch --all` | Fetch configured updates from all configured remotes | **Scan every remote** |
| `git branch -r` | Inspect remote-tracking branches known locally | **What remote branches does my Git know about?** |
| `git merge upstream/<branch>` | Integrate that fetched history into the current branch | **Bring that history into where I am standing** |
| `git status` | Inspect repository/merge/conflict state | **What does Git need from me?** |
| `git add <file>` | During conflict resolution, mark the edited result as resolved/staged | **This file is ready** |
| `git commit` | Complete the merge after resolving conflicts | **Record the resolution** |
| `git merge --abort` | Abandon the current merge attempt | **Back out of this merge** |
| `git push` | Publish the completed local history | **Send the result out** |

---

# The Full Remote Mental Model

```text
                         YOUR COMPUTER

                         main
                           |
                           |
          -------------------------------------
          |                                   |
     origin/main                    upstream/main
                                      upstream/sensor-patch

          ^                                   ^
          | git fetch                         | git fetch
          |                                   |

       origin                              upstream
   personal remote                    Mission Control
```

And:

```text
git fetch --all
```

updates fetched knowledge from both remotes.

Then **you decide** what to integrate.

---

# The Full Workflow

```text
1. INSPECT
   git remote -v

2. FETCH
   git fetch --all

3. INSPECT REMOTE-TRACKING BRANCHES
   git branch -r

4. CHOOSE
   Which fetched history do I actually want?

5. MERGE
   git merge <remote>/<branch>

6. IF CONFLICT
   git status
   edit files
   git add <file>
   git commit

7. PUBLISH
   git push
```

---

# Optional Team Challenge — Three-Pilot Rescue

Use three students.

## Student A — Local Pilot

Makes a local commit.

## Student B — Mission Control

Makes a different non-overlapping remote change.

Student A fetches and merges it successfully.

## Student C — Reactor Engineer

Makes a remote change to the exact line Student A is also changing locally.

Student A fetches again and attempts the merge.

This time the team must resolve the conflict together.

### Team questions

1. Why did the first merge succeed?
2. Why did the second merge stop?
3. Which parts of the process were automatic?
4. Which part required a human decision?
5. When did the remote repository finally receive the resolution?

---

# Instructor Notes & Answer Key

## Debrief Answers

### 1

```bash
git fetch --all
```

### 2

No.

It fetches configured updates from configured remotes and updates remote-tracking information. It does not automatically create a working local branch for every remote branch.

### 3

```bash
git branch -r
```

### 4

Because the student is currently on `main`, Git attempts to integrate the history represented by:

```text
upstream/sensor-patch
```

into the current `main` branch.

### 5

No.

Once the commits and remote-tracking reference have been fetched, the merge operates on locally available Git history.

### 6

No.

Git can often automatically reconcile non-overlapping changes.

### 7

```bash
git status
```

### 8

Suggested answer:

```text
1. Inspect the conflicted file
2. Edit it into the intended final version and remove conflict markers
3. git add <resolved-file>
4. git commit
5. git push
```

### 9

It abandons the current merge attempt and tries to reconstruct the pre-merge state.

### 10

B.

The merge is resolved and committed locally first.

`git push` publishes that completed history afterward.

---

# Accuracy Notes for the Instructor

## 1. `git fetch --all` is about remotes

For beginner teaching, say:

> **Fetch updates from all configured remotes.**

Technically, Git supports configuration that can exclude particular remotes from `--all`.

That edge case is not necessary for the student-facing mission.

The key misconception to prevent is:

```text
git fetch --all
≠ create all local branches
≠ merge all branches
```

---

## 2. `git branch -r` shows remote-tracking branches

Avoid saying:

> "`git branch -r` directly asks GitHub what branches exist right now."

It shows the remote-tracking branches that the local repository currently knows about.

Fetching first makes that information current.

---

## 3. `git remote add` does not automatically imply a normal fetch

The basic command:

```bash
git remote add upstream <url>
```

adds the remote configuration.

The mission therefore follows it with:

```bash
git fetch --all
```

to retrieve remote information.

---

## 4. Remote-tracking branches are local references

Keep reinforcing:

```text
upstream/sensor-patch
```

is not a magical network branch.

It is a local reference representing fetched information from the remote.

That is why this can work without new network communication:

```bash
git merge upstream/sensor-patch
```

after the fetch has already happened.

---

## 5. Merge direction matters

Students frequently reverse this.

If the student is on:

```text
main
```

then:

```bash
git merge upstream/sensor-patch
```

means:

```text
integrate upstream/sensor-patch INTO main
```

A useful classroom sentence is:

> **Git merges the named history into the branch you are currently standing on.**

---

## 6. Divergence is not the same as conflict

Two histories can diverge and still merge automatically.

The clean-merge mission deliberately creates:

```text
local change  -> pilot-notes.txt
remote change -> sensor-related content
```

Those changes do not compete.

The conflict mission deliberately creates:

```text
local  -> Reactor Output: 75
remote -> Reactor Output: 90
```

from the same base line.

That makes the conceptual difference visible.

---

## 7. Conflict markers are a request for human judgment

Do not teach conflict resolution as simply:

> "Delete the markers."

The student must decide the correct final content.

The markers only show the competing regions Git could not automatically reconcile.

---

## 8. `git add` has an important new meaning here

Earlier, students learned:

```text
git add
= stage a change for a commit
```

During conflict resolution, that same staging mechanism also signals:

```text
I have edited this conflicted path into the version
that should be recorded.
```

Then the merge can be completed with:

```bash
git commit
```

or:

```bash
git merge --continue
```

---

## 9. Start merges with a clean working tree

For a controlled classroom exercise, students should commit their intended local work before attempting the merge.

This makes conflict behavior easier to understand and makes `git merge --abort` a safer recovery tool.

---

## 10. A successful merge is still local

After either:

```text
clean merge
```

or:

```text
resolved conflict
```

the resulting history initially exists locally.

Students still need:

```bash
git push
```

to publish that result to their configured publishing remote.

---

# Instructor Preparation Checklist

Before class:

- [ ] Students have completed the push and fetch/pull missions.
- [ ] Each student has a usable `origin`.
- [ ] A second repository exists for the `upstream` remote.
- [ ] `origin` and `upstream` share common history.
- [ ] `upstream/sensor-patch` exists for the clean-merge mission.
- [ ] The sensor patch changes content that does not overlap with `pilot-notes.txt`.
- [ ] Students can edit their own `origin` through the GitHub web UI.
- [ ] Students begin the conflict mission from a clean working tree.
- [ ] The local and remote reactor edits both start from exactly `Reactor Output: 50`.
- [ ] Students do not push the local `75` commit before making the remote `90` commit.
- [ ] Automatic IDE fetch is disabled or explained if it changes the timing of remote-tracking updates.

---

# Recommended Mission Sequence Across All Three Labs

## Remote Basics

```text
clone
remote -v
commit
push
push -u
```

## Incoming Remote Changes

```text
fetch
pull
```

## Multi-Remote & Integration

```text
remote add upstream
fetch --all
branch -r
merge remote-tracking branch
resolve merge conflict
merge --abort
push resolution
```

The progression becomes:

```text
COPY A REMOTE PROJECT
        ↓
LEARN WHERE IT IS CONNECTED
        ↓
PUBLISH LOCAL WORK
        ↓
LEARN ABOUT INCOMING WORK
        ↓
INTEGRATE INCOMING WORK
        ↓
TRACK MULTIPLE REMOTES
        ↓
CHOOSE WHAT TO MERGE
        ↓
RESOLVE HUMAN DISAGREEMENTS
```

---

# Final Memory Map

```text
git remote -v
"Who am I connected to?"

git fetch
"What changed on this remote?"

git fetch --all
"What changed across my configured remotes?"

git branch -r
"What remote-tracking branches does my local Git know about?"

git merge <remote>/<branch>
"Bring that fetched history into my current branch."

MERGE CONFLICT
"Git needs a human decision."

git add <resolved-file>
"This conflict is resolved and staged."

git commit
"Record the resolution."

git merge --abort
"Cancel this merge attempt."

git push
"Publish my completed local history."
```

---

# Official Git References for Instructor Verification

- Git Fetch documentation: https://git-scm.com/docs/git-fetch
- Git Remote documentation: https://git-scm.com/docs/git-remote
- Git Merge documentation: https://git-scm.com/docs/git-merge
- Git Status documentation: https://git-scm.com/docs/git-status

