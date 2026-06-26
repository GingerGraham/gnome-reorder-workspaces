# Project: gnome-reorder-workspaces (Maintained Fork)

## Purpose

Maintain and automate the release lifecycle of the `reorder-workspaces` GNOME Shell
extension, originally by smmr0. The primary goal is automated compatibility updates
when new GNOME Shell versions are released.

## Repository Structure

- `src/` — Extension source (JS, GSchema XML, metadata.json)
- `src/metadata.json` — Contains `shell-version` array and extension `version` integer
- `.github/workflows/` — CI/CD pipelines
- `dist/` — Built extension zip (gitignored)

## Automation Goals (in priority order)

### 1. GNOME Version Detection (`detect-gnome-release.yml`)

Poll the GNOME GitLab API for new `gnome-shell` tags on a weekly schedule.
API endpoint: https://gitlab.gnome.org/api/v4/projects/GNOME%2Fgnome-shell/repository/tags
Detect tags matching patterns: `X.Y`, `X.rc`, `X.beta` where X > current max in metadata.json.
On detection: open a PR that bumps `shell-version` in metadata.json and increments
the extension `version` integer. PR should include the gnome-shell tag that triggered it.

### 2. Build Validation (`install.yml` — already exists)

Runs on every push and PR. Installs extension into real GNOME Shell via xvfb-run.
This must remain passing as a required status check.

### 3. Logic Unit Tests (`test.yml` — to be created)

Run a GJS test harness that imports extension modules with mocked GNOME globals
and exercises: moveWorkspace(), reorderWorkspaceNames(), workspaceIsEmptyDynamic().
Mock objects needed: global.workspace_manager, Shell.ActionMode, Meta.KeyBindingFlags.
On failure: capture output and open a GitHub Issue with label `test-failure` and
attach full logs. Do NOT auto-merge if these fail.

### 4. Functional Smoke Test (`smoke.yml` — aspirational, lower priority)

Under xvfb-run, launch a GNOME Shell nested session, enable the extension,
use gdbus/xdotool to trigger the keybinding, and verify workspace order changed
via gdbus introspection of org.gnome.Shell or the workspace_manager state.
This is the most fragile layer — treat failures as advisory, not blocking,
until the test is proven stable.

### 5. Release Publishing (`release.yml` — to be created)

Trigger: a PR opened by the version-detection workflow is merged.
Action: run build, create a GitHub Release tagged `vX` (extension version),
attach the zip artifact. Then open a tracking issue reminding maintainer
to submit to extensions.gnome.org manually.

## Key Files to Understand

- `src/extension.js` — Main extension logic. ReorderWorkspaces class.
  The `moveWorkspace()` and `reorderWorkspaceNames()` methods are the core logic.
  `workspaceIsEmptyDynamic()` guards against moving the trailing empty workspace
  in dynamic-workspaces mode.
- `src/metadata.json` — `shell-version` is the array to append new GNOME versions to.
  `version` is an integer, increment by 1 per release.
- `src/schemas/` — GSettings schema. Only changes if settings are added/removed.

## Extension Behavior Notes

The extension registers keybindings (default Ctrl+Super+Up/Down) to reorder
the active workspace by swapping it with an adjacent one. It also reorders
the workspace _names_ stored in org.gnome.desktop.wm.preferences to keep
them consistent. It supports three modes for behavior outside the overview:
default (keybindings inactive), reorder (always active), disabled.

The extension has NO side effects if the keybindings are never triggered.
This means the install test passing is a low bar — it only proves the extension
loads without crashing, not that it works.

## Testing Philosophy

Layer 1 (unit) — mock GNOME objects, test JS logic in isolation. Fast, reliable.
Layer 2 (install) — real GNOME Shell loads extension without error. Medium confidence.
Layer 3 (smoke) — synthetic input triggers real workspace reorder. High confidence, fragile.

Aim to make Layer 1 comprehensive enough that Layer 3 is confirmatory, not primary.

## Known Constraints

- EGO (extensions.gnome.org) submission requires manual human review. Cannot be automated.
- GNOME Shell's JS runtime (GJS) is not Node.js. Unit tests must either use GJS directly
  or carefully mock all gi:// imports for a Node.js runner.
- The existing build script requires gnome-extensions CLI tool (part of gnome-shell package).
- Workflow uses xvfb-run for the virtual display. Wayland headless is an alternative
  worth exploring for GNOME >= 45 which is increasingly Wayland-native.

## Workflow for a Typical Version Bump Release

1. Detection workflow fires, opens PR: "Add GNOME 50 to shell-version"
2. CI runs build + unit tests on the PR
3. If all green: maintainer reviews and merges (or enables auto-merge)
4. Release workflow fires, publishes GitHub Release
5. Maintainer submits zip to EGO
6. Tracking issue closed when EGO version goes live

## Workflow for a Breaking Change Release

1. Smoke test or user report indicates extension broken on new GNOME version
2. GitHub Issue opened with test logs attached
3. Maintainer investigates, makes code changes
4. Normal PR/review/release flow resumes
