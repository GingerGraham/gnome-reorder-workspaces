---
name: commit-message
description: >
  Write git commit messages following the Conventional Commits specification,
  compatible with semantic-release tooling. Use this skill whenever the user
  asks to write, review, or fix a commit message, or when staging/committing
  changes as part of any workflow in this repo. Also use it when the user says
  "commit this", "what should my commit message be", or asks about versioning
  semantics implied by a change.
---

# Commit Message Skill

Commit messages in this repo follow the [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)
specification. This enables automated changelog generation and semantic version
bumping via the release workflow.

---

## Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Rules

- **Subject line**: 72 characters max, imperative mood ("add" not "added"), no trailing period, lowercase after the colon
- **Body**: wrap at 100 characters (matches `.editorconfig`), explain *why* not *what*
- **Footer**: `BREAKING CHANGE:` footer triggers a major version bump; `Refs #N` or `Closes #N` links issues

---

## Types

| Type       | When to use                                                        | Version bump |
|------------|--------------------------------------------------------------------|--------------|
| `feat`     | New user-facing feature or new capability                          | minor        |
| `fix`      | Bug fix, behavioural correction                                    | patch        |
| `chore`    | Maintenance: deps, build tooling, CI config, metadata bumps        | none         |
| `docs`     | Documentation only                                                 | none         |
| `refactor` | Code change that neither fixes a bug nor adds a feature            | none         |
| `test`     | Adding or updating tests                                           | none         |
| `ci`       | Changes to GitHub Actions workflows                                | none         |
| `perf`     | Performance improvement                                            | patch        |
| `revert`   | Reverts a previous commit (use `git revert` then amend the message)| varies       |

> `BREAKING CHANGE:` in the footer of **any** type triggers a **major** bump regardless of type.

---

## Scopes (this repo)

Use a scope when the change is clearly bounded to one area. Omit it for cross-cutting changes.

| Scope        | Covers                                              |
|--------------|-----------------------------------------------------|
| `extension`  | `src/extension.js` — core reorder logic             |
| `prefs`      | `src/prefs.js`, `AccelRow.js`, `KeybindingBehaviorOutsideOfOverviewRow.js` |
| `schema`     | `src/schemas/*.gschema.xml`                         |
| `metadata`   | `src/metadata.json` — version/shell-version changes |
| `ci`         | `.github/workflows/`                                |
| `build`      | `package.json` build/pack scripts                   |
| `deps`       | Dependency updates (`yarn.lock`, `package.json`)    |
| `docs`       | `README.md`, `docs/`, `CHANGELOG.md`                |

---

## Examples

### Routine version bump (automated PR)
```
chore(metadata): add GNOME 50 to shell-version

Bumps shell-version array to include "50" and increments extension
version from 25 to 26.

Triggered by gnome-shell tag: 50.rc
```

### Bug fix
```
fix(extension): prevent reorder of trailing empty workspace in static mode

workspaceIsEmptyDynamic() was returning false when dynamic-workspaces
was disabled, allowing the last workspace to be moved even when empty.
Guard now also checks the static workspace count.

Closes #42
```

### New feature
```
feat(prefs): add left/right keybinding axis for horizontal workspace layouts

Adds move-workspace-left and move-workspace-next settings mirroring the
existing up/down bindings, for use with horizontal workspace arrangements
(GNOME default since 40).
```

### Breaking change
```
refactor(schema): rename keybinding-behavior-outside-of-overview setting

The setting key is renamed from keybinding-behavior-outside-of-overview
to outside-overview-behavior to reduce verbosity.

BREAKING CHANGE: Users with a custom value for the old key will have
their preference reset to the default on upgrade.
```

### CI / workflow change
```
ci: add detect-gnome-release workflow for automated version bump PRs

Weekly schedule polls the GNOME GitLab API for new gnome-shell tags.
On detection of X.Y / X.rc / X.beta tags newer than the current max
in metadata.json, opens a PR with the appropriate metadata bump.
```

---

## What to avoid

- Vague subjects: `fix bug`, `update stuff`, `changes` — always say *what* was fixed/updated/changed
- WIP messages on PRs: squash or amend before merging
- Mixing unrelated changes in one commit — each commit should be a single logical unit
- Past tense: `added feature` → `add feature`
- Capitalising the subject after the colon: `feat: Add thing` → `feat: add thing`

---

## Multi-commit PR guidance

For PRs with multiple commits, each commit should be independently meaningful.
The PR title should itself be a valid Conventional Commits message, as it becomes
the squash commit message if the PR is squash-merged.
