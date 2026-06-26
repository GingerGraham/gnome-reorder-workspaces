# Fork Workflow

This repo uses a split-branch strategy to keep a clean separation between upstream changes and local work, avoiding merge conflict hell when rebasing against `upstream/main`.

## Branch Structure

| Branch      | Tracks             | Purpose                                         |
| ----------- | ------------------ | ----------------------------------------------- |
| `main`      | `upstream/main`    | Read-only mirror of upstream; fast-forward only |
| `graham/main` | `origin/graham/main` | Local working branch; GitHub default            |

## Initial Setup

These steps were run once when the fork was first cloned.

**1. Create and push the working branch**

```bash
git checkout -b graham/main
git push origin graham/main
```

**2. Set `graham/main` as the GitHub default branch**

```bash
gh repo edit GingerGraham/gnome-reorder-workspaces --default-branch graham/main
```

Or via the UI: *Settings → General → Default branch → Switch to `graham/main`*

**3. Point local `main` at upstream instead of origin**

```bash
git branch --set-upstream-to=upstream/main main
```

**4. Set tracking for the working branch**

```bash
git branch --set-upstream-to=origin/graham/main graham/main
```

## Day-to-Day Workflow

### Sync upstream changes into your working branch

```bash
git fetch upstream
git checkout graham/main
git rebase upstream/main
git push origin graham/main --force-with-lease
```

### Keep local `main` as an upstream mirror (optional)

```bash
git checkout main
git merge --ff-only upstream/main
```

> `--ff-with-lease` on the push protects against accidentally overwriting someone else's work if the remote has diverged unexpectedly.

## Relevant Remotes

```
origin    https://github.com/GingerGraham/gnome-reorder-workspaces.git  (fork)
upstream  https://github.com/smmr0/gnome-reorder-workspaces.git         (source)
```