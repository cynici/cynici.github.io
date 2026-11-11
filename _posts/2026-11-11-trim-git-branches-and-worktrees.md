---
layout: post
title: "Trim git branches and worktrees"
date: 2026-11-11 08:00:00 -0000
categories: engineering git
---

"Anyone's Claude chowing your storage?" The question came up in a team chat recently, and the answer arrived within minutes: watch out for worktrees.

### The problem

AI coding agents such as Claude Code and Codex like to work in [git worktrees](https://git-scm.com/docs/git-worktree). Each session gets its own directory with its own branch, so parallel tasks never trample each other's files. That isolation is great while the work is in progress.

The trouble starts after the pull request merges. The harnesses are sloppy about cleaning up after themselves, so you are left with:

* **A full checkout per worktree.** Every worktree is a complete copy of the working tree. Add `node_modules`, build output and virtualenvs, and each one easily runs to hundreds of megabytes or more.
* **A local branch per worktree.** Git refuses to delete a branch that a worktree has checked out, so the branches pile up too.
* **Branches that look unmerged.** Most teams squash-merge on GitHub. A squash merge creates a new commit on the default branch, so `git branch --merged main` never lists the feature branch, and the usual cleanup one-liners leave it alone.

Run a few agent sessions a day for a few weeks and you have dozens of stale worktrees eating your disk. Some people give up on worktrees altogether just to save space, which throws away the isolation that made them worth using.

### The solution: `git trim`

A colleague shared the script they use, wired up as a git alias that both they and their agents call, and run on a weekly cleanup schedule. It deletes local branches whose work has landed, and first removes any worktree that has them checked out.

A branch counts as **landed** when either:

* `git branch --merged <default branch>` lists it (a merge commit or a fast-forward), or
* GitHub has a **merged pull request** from this repository whose head commit is *exactly* the branch's tip. This is what catches squash merges.

The exact-tip comparison is the clever part. A branch name that was reused after a merge, or a branch that gained new commits after its pull request merged, does not match and is kept.

It is also careful about what it will not touch:

* the main worktree and the branch you are on,
* locked worktrees,
* worktrees with uncommitted or untracked changes.

And it deliberately never runs `git worktree prune`, because that would also drop the entries other tools keep for worktrees whose directory is temporarily missing.

### Install

Save the script below as `~/.local/bin/git-trim-branches`, make it executable, and make sure `~/.local/bin` is on your `PATH`:

```bash
chmod +x ~/.local/bin/git-trim-branches
```

Then add the alias to `~/.gitconfig`:

```ini
[alias]
	trim = !git-trim-branches
```

Squash-merge detection needs the [GitHub CLI](https://cli.github.com/) installed and signed in (`gh auth login`). Without it, the script still works but only finds branches that `git branch --merged` can see.

### Usage

Always start with a dry run:

```bash
git trim --dry-run
```

```
would remove worktree /Users/me/src/app/.claude/worktrees/fix-login-a1b2c3
would delete branch claude/fix-login-a1b2c3 (its pull request merged)
kept     claude/new-feature-d4e5f6: its worktree has uncommitted changes (/Users/me/src/app/.claude/worktrees/new-feature-d4e5f6)
git trim: dry run, 1 branch(es) would be removed, 1 kept
```

When the list looks right, run it for real:

```bash
git trim
```

Add `--closed` to also treat pull requests that were closed *without* merging as finished, using the same exact-tip check.

To tell an agent to use it, a line in your `CLAUDE.md` (or equivalent) is enough, for example: *"After a pull request merges, run `git trim` in the repository to remove its worktree and branch."*

### The script

```bash
#!/usr/bin/env bash
# git-trim-branches: delete local branches whose work has landed, and the worktrees that have
# them checked out. Called by the `trim` alias in ~/.gitconfig.
#
# A branch counts as landed when either:
#   - `git branch --merged <default branch>` lists it (merge commit or fast-forward), or
#   - GitHub has a merged pull request from this repository whose head commit is exactly the
#     branch's tip. This catches squash merges, which `git branch --merged` cannot see. The
#     commit comparison means a branch name reused after a merge, or a branch that gained
#     commits after its pull request merged, is kept.
# With --closed, a pull request closed without merging counts as well, under the same
# tip comparison.
#
# For each landed branch the script removes its worktree first, because Git refuses to delete a
# branch that a worktree has checked out. A worktree with uncommitted or untracked changes, a
# locked worktree, the main worktree, and the branch you are on are always skipped.
#
# This script never runs `git worktree prune`: it would also drop the entries that other tools
# (T3 Code, Codex) keep for worktrees whose directory is temporarily missing.

set -u

usage() {
  cat <<'EOF'
Usage: git trim [-n | --dry-run] [--closed]

  -n, --dry-run   Print what would be removed and change nothing.
      --closed    Also treat pull requests closed without merging as finished.
EOF
}

dry_run=0
include_closed=0
for arg in "$@"; do
  case "$arg" in
    -n | --dry-run) dry_run=1 ;;
    --closed) include_closed=1 ;;
    -h | --help)
      usage
      exit 0
      ;;
    *)
      usage >&2
      exit 2
      ;;
  esac
done

git rev-parse --git-dir >/dev/null 2>&1 || {
  echo "git trim: not inside a Git repository" >&2
  exit 1
}

git fetch --prune || echo "git trim: fetch failed, continuing with the refs this clone has" >&2

# The branch that other branches merge into, and the branch this worktree has checked out.
default_branch=$(git symbolic-ref --quiet --short refs/remotes/origin/HEAD 2>/dev/null | sed 's|^origin/||')
if [ -z "$default_branch" ]; then
  if git show-ref --verify --quiet refs/heads/main; then default_branch=main; else default_branch=master; fi
fi
current_branch=$(git symbolic-ref --quiet --short HEAD 2>/dev/null || true)

# The first entry of `git worktree list` is the main worktree.
main_worktree=$(git worktree list --porcelain | sed -n '1s/^worktree //p')

have_gh=1
if ! command -v gh >/dev/null 2>&1 || ! gh auth status >/dev/null 2>&1; then
  have_gh=0
  echo "git trim: gh is missing or not signed in, so squash-merged branches are not detected" >&2
fi

# Prints "<path>|<locked 0 or 1>" for the worktree that has $1 checked out, or nothing.
worktree_for_branch() {
  git worktree list --porcelain | awk -v want="refs/heads/$1" '
    /^worktree / { path = substr($0, 10); locked = 0 }
    /^locked/    { locked = 1 }
    /^branch /   { if (substr($0, 8) == want) { print path "|" locked; exit } }
  '
}

# Prints "merged" or "closed" when a pull request from this repository has this branch's exact
# tip as its head, otherwise nothing. $1 is the branch, $2 its tip commit.
pr_state_for_tip() {
  [ "$have_gh" -eq 1 ] || return 0
  local states="merged" state
  [ "$include_closed" -eq 1 ] && states="merged closed"
  for state in $states; do
    if [ -n "$(gh pr list --state "$state" --head "$1" --limit 30 \
      --json headRefOid,isCrossRepository \
      --jq ".[] | select(.isCrossRepository == false and .headRefOid == \"$2\") | .headRefOid" 2>/dev/null | head -n 1)" ]; then
      echo "$state"
      return 0
    fi
  done
}

merged_locally=$(git branch --merged "$default_branch" --format='%(refname:short)' 2>/dev/null)

removed=0
kept=0
while IFS= read -r branch; do
  [ -n "$branch" ] || continue
  [ "$branch" = "$default_branch" ] && continue
  [ "$branch" = "$current_branch" ] && continue

  tip=$(git rev-parse "refs/heads/$branch")
  reason=""
  delete_flag="-D"
  if printf '%s\n' "$merged_locally" | grep -Fxq -- "$branch"; then
    reason="merged into $default_branch"
    delete_flag="-D"
  else
    state=$(pr_state_for_tip "$branch" "$tip")
    case "$state" in
      merged) reason="its pull request merged" ;;
      closed) reason="its pull request closed without merging" ;;
    esac
  fi
  [ -n "$reason" ] || continue

  entry=$(worktree_for_branch "$branch")
  if [ -n "$entry" ]; then
    wt_path=${entry%|*}
    wt_locked=${entry##*|}
    if [ "$wt_path" = "$main_worktree" ]; then
      echo "kept     $branch: it is checked out in the main worktree"
      kept=$((kept + 1))
      continue
    fi
    if [ "$wt_locked" = "1" ]; then
      echo "kept     $branch: its worktree is locked ($wt_path)"
      kept=$((kept + 1))
      continue
    fi
    if [ -n "$(git -C "$wt_path" status --porcelain 2>/dev/null)" ]; then
      echo "kept     $branch: its worktree has uncommitted changes ($wt_path)"
      kept=$((kept + 1))
      continue
    fi
    if [ "$dry_run" -eq 1 ]; then
      echo "would remove worktree $wt_path"
    else
      git worktree remove "$wt_path" || {
        echo "kept     $branch: git worktree remove failed ($wt_path)"
        kept=$((kept + 1))
        continue
      }
      echo "removed  worktree $wt_path"
    fi
  fi

  if [ "$dry_run" -eq 1 ]; then
    echo "would delete branch $branch ($reason)"
  else
    git branch "$delete_flag" "$branch" >/dev/null && echo "deleted  branch $branch ($reason)"
  fi
  removed=$((removed + 1))
done < <(git for-each-ref --format='%(refname:short)' refs/heads)

if [ "$dry_run" -eq 1 ]; then
  echo "git trim: dry run, $removed branch(es) would be removed, $kept kept"
else
  echo "git trim: $removed branch(es) removed, $kept kept"
fi
```

### Takeaways

* Agent worktrees are cheap to create and expensive to forget. Clean them up on a schedule, not when the disk is full.
* `git branch --merged` is blind to squash merges. Ask the forge (here, GitHub) instead, and compare exact commits, not just branch names.
* Make the cleanup safe by default: dry run first, and never touch dirty, locked or current worktrees.
