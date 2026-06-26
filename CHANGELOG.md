# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Extension versions correspond to the `version` field in `src/metadata.json`.

## [Unreleased]

### Added
- `docs/skills/` — project-specific Claude skills for commit messages, PR descriptions,
  changelog maintenance, and release process
- `docs/repo-fork-workflow.md` — fork and branch strategy documentation

### Changed
### Fixed
### Removed

## [25] - (upstream)

### Added
- GNOME Shell 45, 46, 47, 48, and 49 supported in `shell-version`
- Preferences UI: configurable keybindings via `AccelRow`
- Preferences UI: outside-overview behaviour selector via `KeybindingBehaviorOutsideOfOverviewRow`
- Three outside-overview modes: `default`, `reorder`, `disabled`
- Workspace switcher popup shown when reordering outside overview
- Build and install scripts via `yarn` / `gnome-extensions pack`
- GitHub Actions: `install.yml` build validation workflow
- GitHub Actions: `lint.yml` ESLint workflow

[Unreleased]: https://github.com/GingerGraham/gnome-reorder-workspaces/compare/v25...HEAD
[25]: https://github.com/GingerGraham/gnome-reorder-workspaces/releases/tag/v25
