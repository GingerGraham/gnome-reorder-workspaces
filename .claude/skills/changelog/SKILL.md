---
name: changelog
description: >
  Maintain and update CHANGELOG.md for the gnome-reorder-workspaces repo following
  Keep a Changelog conventions. Use this skill whenever the user asks to update the
  changelog, add a changelog entry, prepare a release, or when any workflow step
  (version bump PR, release publishing) requires a changelog update. Also use it
  when the user asks what changed in a version or how to format changelog entries.
---

# Changelog Skill

This repo's `CHANGELOG.md` follows [Keep a Changelog 1.0.0](https://keepachangelog.com/en/1.0.0/)
and [Semantic Versioning 2.0.0](https://semver.org/). The extension's `version`
field in `metadata.json` is an integer (not semver), but the changelog uses `vN`
tags that correspond 1:1 to that integer.

---

## File Location and Format

`CHANGELOG.md` lives at the repo root. Structure:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Extension versions correspond to the `version` field in `src/metadata.json`.

## [Unreleased]

### Added
### Changed
### Fixed
### Removed
### Security
### Deprecated

## [26] - 2025-06-15

### Added
- GNOME Shell 50 added to supported shell-version list (#12)

## [25] - 2025-01-10

...

[Unreleased]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v26...HEAD
[26]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v25...v26
[25]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v24...v25
```

---

## Section Guidance

Use only the sections that have content. Omit empty sections entirely from released versions
(they can remain as empty stubs under `[Unreleased]` for convenience).

| Section      | What goes here                                                                 |
|--------------|--------------------------------------------------------------------------------|
| `Added`      | New features, new supported GNOME versions, new settings                       |
| `Changed`    | Changes to existing behaviour, keybinding defaults, setting renames            |
| `Fixed`      | Bug fixes — reference the issue number where possible                         |
| `Removed`    | Dropped GNOME version support, removed settings, deleted files                 |
| `Security`   | Security fixes (rare for a GNOME extension, but include if relevant)           |
| `Deprecated` | Features that will be removed in a future version                              |

---

## Entry Style

- Write in **past tense**, third person: "Added X", "Fixed Y", not "Add X", "Fix Y"
  (this is the opposite of commit messages — changelog entries describe what *was* done)
- Link to the PR or issue in parentheses: `Fixed crash on GNOME 49 (#38)`
- One entry per logical change — don't bundle unrelated things on one line
- Keep entries concise: one line is ideal, two maximum
- For GNOME version additions, always note the gnome-shell tag that triggered it

---

## Workflow: Adding an Entry

### During development (any PR that isn't a version bump)

Add a line under `## [Unreleased]` in the appropriate section as part of the PR.
Never leave `[Unreleased]` empty if the PR has user-visible or operator-visible changes.

Example — adding to a bug fix PR:
```markdown
## [Unreleased]

### Fixed
- Prevented reordering of the trailing empty workspace when dynamic workspaces is enabled (#42)
```

### During a version bump PR (automated)

The detect-gnome-release workflow PR should:

1. Move all content from `[Unreleased]` sections into a new versioned section
2. Add the GNOME version support entry to `Added`
3. Reset `[Unreleased]` to empty stubs
4. Add the new version link at the bottom of the file
5. Update the `[Unreleased]` comparison link to point to the new tag

Example transformation — before:
```markdown
## [Unreleased]

### Fixed
- Prevented reordering of trailing empty workspace (#42)
```

After (bumping to v26 with GNOME 50 support):
```markdown
## [Unreleased]

### Added
### Changed
### Fixed
### Removed

## [26] - 2025-06-15

### Added
- GNOME Shell 50 added to supported shell-version list (gnome-shell tag: `50.rc`)

### Fixed
- Prevented reordering of trailing empty workspace (#42)
```

And add to the bottom link list:
```markdown
[Unreleased]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v26...HEAD
[26]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v25...v26
```

---

## Version Numbering Reference

The extension `version` integer in `metadata.json` is the single source of truth.
The changelog version tag is `[N]` (no semver, just the integer). GitHub Releases
are tagged `vN` (e.g. `v26`).

There is no semantic versioning for this extension — upstream EGO only tracks
the integer version. Use the commit message conventions to *signal* the nature
of a change (feat/fix/breaking), but the version increment is always +1.

---

## Initial CHANGELOG.md

When creating the file from scratch (first time in this repo), use this as the
seed — fill in the v25 section from the git log:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Extension versions correspond to the `version` field in `src/metadata.json`.

## [Unreleased]

### Added
### Changed
### Fixed
### Removed

## [25] - YYYY-MM-DD

### Added
- Initial maintained fork with automated release workflows
- GNOME Shell 45, 46, 47, 48, and 49 supported

[Unreleased]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v25...HEAD
[25]: https://github.com/GingerGraham/gnome-reorder-workspaces/releases/tag/v25
```
