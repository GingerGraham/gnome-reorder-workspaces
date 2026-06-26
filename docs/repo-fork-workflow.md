# Fork Workflow

This repo uses a split-branch strategy to keep a clean separation between upstream changes and local work, avoiding merge conflict hell when rebasing against `upstream/main`.

Wherever `user/main` is mentioned, replace `user` with your name, GitHub username, or other identifier. This is the branch you will work on and push to your fork. The `main` branch is a read-only mirror of the upstream repository.

e.g. I might setup `graham/main` as my default working branch, while `main` tracks `upstream/main`. This allows me to rebase my work against upstream without worrying about merge conflicts.

## Branch Structure

| Branch      | Tracks             | Purpose                                         |
| ----------- | ------------------ | ----------------------------------------------- |
| `main`      | `upstream/main`    | Read-only mirror of upstream; fast-forward only |
| `user/main` | `origin/user/main` | Local working branch; GitHub default            |

## Initial Setup

These steps were run once when the fork was first cloned.

**1. Create and push the working branch**

```bash
git checkout -b user/main
git push origin user/main
```

**2. Set `user/main` as the GitHub default branch**

```bash
gh repo edit GingerGraham/gnome-reorder-workspaces --default-branch user/main
```

Or via the UI: _Settings → General → Default branch → Switch to `user/main`_

**3. Point local `main` at upstream instead of origin**

```bash
git branch --set-upstream-to=upstream/main main
```

**4. Set tracking for the working branch**

```bash
git branch --set-upstream-to=origin/user/main user/main
```

## Day-to-Day Workflow

### Sync upstream changes into your working branch

```bash
git fetch upstream
git checkout user/main
git rebase upstream/main
git push origin user/main --force-with-lease
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
