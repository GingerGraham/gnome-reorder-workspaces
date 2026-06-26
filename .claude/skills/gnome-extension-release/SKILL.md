---
name: gnome-extension-release
description: >
  Guide the release process for the gnome-reorder-workspaces GNOME Shell extension.
  Use this skill whenever the user asks to cut a release, publish a version, prepare
  a GitHub Release, or submit to extensions.gnome.org (EGO). Also use it when any
  release.yml workflow step is being authored or debugged, or when the user asks
  "what do I do after merging a version bump PR".
---

# GNOME Extension Release Skill

Releases for this extension happen at two layers: **GitHub Release** (automated)
and **EGO (extensions.gnome.org) submission** (manual, always). This skill covers
both, plus the workflow that connects them.

---

## Release Triggers

A release is created when a PR from the `detect-gnome-release` workflow is merged
into the default branch (`graham/main` or equivalent). The `release.yml` workflow
fires on that merge event and handles the GitHub side automatically.

Manual releases (e.g. for bug fixes) follow the same process but are triggered
by merging any PR that bumps the `version` field in `metadata.json`.

---

## The Release Workflow (`release.yml`)

The workflow does the following in order:

1. **Detect version** — reads `version` from `src/metadata.json`
2. **Build** — runs `yarn run build`, produces `dist/reorder-workspaces@jer.dev.shell-extension.zip`
3. **Create GitHub Release** — tagged `vN` (e.g. `v26`), title `v26`, attaches the zip
4. **Open tracking issue** — opens a GitHub Issue reminding the maintainer to submit to EGO

### Release detection logic

The workflow should only fire when the `version` integer has actually changed relative
to the previous commit on the default branch. Compare `git show HEAD~1:src/metadata.json`
vs the current file to confirm a bump occurred before creating a release.

### GitHub Release body template

```markdown
## What's New

<!-- Pull from CHANGELOG.md for this version's section -->

## Supported GNOME Shell Versions

<!-- Pull from shell-version array in metadata.json -->

## Installation

Install from [extensions.gnome.org](https://extensions.gnome.org/extension/3685/reorder-workspaces/)
or download the zip below and run:

```bash
gnome-extensions install reorder-workspaces@jer.dev.shell-extension.zip
gnome-extensions enable reorder-workspaces@jer.dev
```

> **Note:** The EGO listing may lag behind this GitHub Release by a few days
> pending manual review.
```

---

## EGO Submission (Manual)

EGO does **not** allow automated submission — every version requires a human to
log in and upload. This cannot be changed. The tracking issue opened by `release.yml`
is the reminder mechanism.

### Tracking Issue Template

```markdown
Title: Submit v[VERSION] to extensions.gnome.org

## EGO Submission Checklist

- [ ] GitHub Release v[VERSION] is published with zip attached
- [ ] Log in to https://extensions.gnome.org/ with the maintainer account
- [ ] Navigate to the extension: https://extensions.gnome.org/extension/3685/reorder-workspaces/
- [ ] Upload `reorder-workspaces@jer.dev.shell-extension.zip` from the GitHub Release
- [ ] Set supported GNOME versions to match `shell-version` in metadata.json:
      [LIST FROM METADATA]
- [ ] Submit for review
- [ ] Wait for EGO approval (typically 1–7 days)
- [ ] Close this issue once the new version is live on EGO

## Links

- GitHub Release: https://github.com/GingerGraham/gnome-reorder-workspaces/releases/tag/v[VERSION]
- EGO listing: https://extensions.gnome.org/extension/3685/reorder-workspaces/

/label ~"ego-submission"
```

---

## Build Verification Before Release

Before any release, confirm these pass locally or in CI:

```bash
# Lint
yarn run lint

# Build
yarn run build

# Verify zip contents
unzip -l dist/reorder-workspaces@jer.dev.shell-extension.zip
```

The zip should contain:
- `extension.js`
- `prefs.js`
- `AccelRow.js`
- `KeybindingBehaviorOutsideOfOverviewRow.js`
- `metadata.json`
- `schemas/org.gnome.shell.extensions.reorder-workspaces.gschema.xml`
- `LICENSE`

Missing any of these → the build script has a problem; do not release.

---

## Version Consistency Check

Before tagging, verify these three values are consistent:

| Source                   | Field              | Example |
|--------------------------|--------------------|---------|
| `src/metadata.json`      | `version`          | `26`    |
| GitHub Release tag       | tag name           | `v26`   |
| `CHANGELOG.md`           | latest version     | `[26]`  |

If any of these are out of sync, fix before creating the release.

---

## Post-Release

After the GitHub Release is created and the EGO submission is done:

1. Close the tracking issue with a comment noting the EGO live date
2. Verify the EGO listing shows the correct supported GNOME versions
3. Check that the `[Unreleased]` comparison link in `CHANGELOG.md` points to the new tag

---

## Hotfix / Out-of-Cycle Release

If a critical bug needs a fix between version bumps:

1. Branch from default: `fix/<description>`
2. Fix the bug, add test, update `CHANGELOG.md` under `[Unreleased]`
3. PR → merge → the version bump *for the fix* is a separate PR
   (increment `version` in `metadata.json` manually as part of the fix PR, or open
   a follow-up bump PR immediately after)
4. `release.yml` fires on the merge of the version-incrementing PR

Do **not** cherry-pick onto a release tag. Always release from the default branch tip.
