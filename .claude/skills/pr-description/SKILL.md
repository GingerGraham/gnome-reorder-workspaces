---
name: pr-description
description: >
  Write or review GitHub Pull Request descriptions for the gnome-reorder-workspaces
  repo. Use this skill whenever the user asks to open a PR, write a PR description,
  review a PR, or when a workflow step involves creating a pull request (e.g. the
  detect-gnome-release automation). Covers title format, body sections, and
  labels/reviewers conventions for this specific repo.
---

# PR Description Skill

Pull requests in this repo fall into a small number of well-defined categories.
Match the PR to a category first, then fill the appropriate template.

---

## PR Categories

| Category           | Trigger                                      | Auto-merge eligible? |
|--------------------|----------------------------------------------|----------------------|
| **Version bump**   | New GNOME Shell version detected by CI       | Yes, if all checks green |
| **Bug fix**        | Behavioural correction to extension logic    | No — maintainer review required |
| **Feature**        | New user-visible capability                  | No — maintainer review required |
| **CI/tooling**     | Workflow, build, or lint changes             | No — maintainer review required |
| **Docs**           | README, CHANGELOG, docs/ only                | Yes, if all checks green |
| **Dependency bump**| `yarn.lock` / `package.json` dep update      | Yes, if all checks green |

---

## Title Format

PR titles must be valid [Conventional Commits](../commit-message/SKILL.md) messages —
they become the squash-commit message on merge.

```
<type>(<scope>): <subject>
```

Examples:
- `chore(metadata): add GNOME 50 to shell-version`
- `fix(extension): guard against reordering empty dynamic workspace`
- `ci: add release.yml publish workflow`

---

## Templates

### Version Bump (automated)

Used by the `detect-gnome-release` workflow. Fill in the bracketed values.

```markdown
## Summary

Adds GNOME Shell [VERSION] to the supported `shell-version` list in `metadata.json`
and increments the extension version to [NEW_VERSION].

Triggered by gnome-shell tag: `[GNOME_TAG]`

## Changes

- `src/metadata.json`: appended `"[VERSION]"` to `shell-version` array
- `src/metadata.json`: bumped `version` from [OLD] to [NEW]
- `CHANGELOG.md`: entry added for v[NEW]

## Checklist

- [ ] `install.yml` passes (extension loads on GNOME [VERSION])
- [ ] `test.yml` passes (unit tests green)
- [ ] `lint.yml` passes

## Notes

> This PR was opened automatically by the `detect-gnome-release` workflow.
> If the extension has breaking behaviour on this GNOME version, close this PR
> and open a new issue with label `breaking-change` before investigating.
```

---

### Bug Fix

```markdown
## Problem

<!-- One paragraph: what was broken, how to reproduce, what the impact was -->

## Solution

<!-- One paragraph: what changed and why this fixes it -->

## Changes

<!-- File-by-file bullet list of what changed -->

## Testing

<!-- How was this tested? Which layer (unit / install / smoke)? -->

## Checklist

- [ ] `install.yml` passes
- [ ] `test.yml` passes (new test added for the regression if possible)
- [ ] `lint.yml` passes
- [ ] `CHANGELOG.md` updated under `[Unreleased]`
- [ ] Commit messages follow Conventional Commits

## Related Issues

Closes #
```

---

### Feature

```markdown
## Summary

<!-- What does this add and why is it useful? -->

## Changes

<!-- File-by-file bullet list -->

## Behaviour

<!-- Before/after description or screenshots if UI changed -->

## Testing

<!-- Unit tests added? Smoke tested? How? -->

## Checklist

- [ ] `install.yml` passes
- [ ] `test.yml` passes
- [ ] `lint.yml` passes
- [ ] Schema updated if new settings added
- [ ] `CHANGELOG.md` updated under `[Unreleased]`
- [ ] Commit messages follow Conventional Commits

## Related Issues

Refs #
```

---

### CI / Tooling

```markdown
## Summary

<!-- What workflow/script/tool changed and why -->

## Changes

<!-- File-by-file bullet list -->

## Verification

<!-- How was this tested? e.g. "Ran workflow manually via workflow_dispatch on branch" -->

## Checklist

- [ ] All existing workflows still pass
- [ ] New workflows have been manually triggered and verified
- [ ] `CHANGELOG.md` updated if this affects the release process
```

---

## Labels

Apply labels when opening PRs manually. The automation workflow applies its own labels.

| Label              | When to apply                                    |
|--------------------|--------------------------------------------------|
| `automated`        | PR opened by a GitHub Actions workflow           |
| `version-bump`     | `shell-version` / extension version change       |
| `breaking-change`  | Requires BREAKING CHANGE in commit footer        |
| `bug`              | Bug fix                                          |
| `enhancement`      | New feature                                      |
| `ci`               | Workflow / build tooling changes                 |
| `documentation`    | Docs-only changes                                |
| `dependencies`     | Dependency updates                               |
| `test-failure`     | Applied to Issues opened on test failure (not PRs)|

---

## Branch Naming

```
<type>/<short-description>
```

Examples:
- `feat/horizontal-workspace-bindings`
- `fix/empty-workspace-guard`
- `chore/gnome-50-version-bump`
- `ci/release-workflow`

Automated PRs from the detection workflow use: `chore/gnome-<VERSION>-version-bump`

---

## Review Expectations

- Version bump PRs with all-green CI may be auto-merged (configure branch protection accordingly)
- All other PRs require at least one maintainer approval
- Squash merge is the default strategy — the PR title becomes the commit message
- Do not merge with failing `install.yml` or `test.yml` checks under any circumstances
