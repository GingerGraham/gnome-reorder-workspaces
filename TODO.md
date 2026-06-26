# TODO

Planned work for the `gnome-reorder-workspaces` maintained fork. Ordered roughly
by priority within each section. Check items off as PRs merge; move completed
items to [`CHANGELOG.md`](CHANGELOG.md) under the appropriate version.

Items marked 🤖 are fully automatable. Items marked 👤 require human action.
Items marked 📋 are meta/process work (skills, templates, docs).

---

## CI/CD Workflows

### To create

- [ ] 🤖 **`detect-gnome-release.yml`** — weekly poll of the GNOME GitLab API for
      new `gnome-shell` tags (`X.Y`, `X.rc`, `X.beta` where X > current max in
      `metadata.json`). On detection: bump `shell-version` array and increment
      `version` integer in `metadata.json`, update `CHANGELOG.md`, open a PR with
      label `automated version-bump`.
      API: `https://gitlab.gnome.org/api/v4/projects/GNOME%2Fgnome-shell/repository/tags`
      Skill: `CLAUDE.md` § Automation Goals #1

- [ ] 🤖 **`test.yml`** — unit tests for core extension logic using a Node.js runner
      with mocked `gi://` imports. Must cover:
  - `moveWorkspace()` — boundary conditions (first/last workspace, empty workspace)
  - `reorderWorkspaceNames()` — name array splice/pad/trim logic
  - `workspaceIsEmptyDynamic()` — dynamic vs static workspace modes
    On failure: open a GitHub Issue using `test_failure` template, attach logs.
    Must be a required status check before `release.yml` can run.
    Note: add `test` to branch protection required checks once this workflow exists.

- [ ] 🤖 **`release.yml`** — triggered when a PR that bumps `version` in
      `metadata.json` is merged into `graham/main`. Steps: read version, build zip,
      create GitHub Release tagged `vN` with zip attached, open EGO submission
      tracking issue using `ego_submission` template.
      Skill: `.claude/skills/gnome-extension-release/SKILL.md`

- [ ] 🤖 **`node-eol.yml`** — scheduled check (monthly) against the Node.js EOL
      data at `https://endoflife.date/api/nodejs.json`. If the version in
      `.node-version` is within 90 days of EOL or already EOL, open a GitHub Issue
      (or PR) to bump to the next LTS. Should also check whether the current version
      is not LTS and flag that.
      Skill: `.claude/skills/node-version-sync/SKILL.md`

### Aspirational / lower priority

- [ ] 🤖 **`smoke.yml`** — functional test under `xvfb-run`. Launch GNOME Shell
      nested session, enable extension, use `gdbus`/`xdotool` to trigger keybinding,
      verify workspace order changed via `gdbus` introspection of
      `org.gnome.Shell` / `workspace_manager`. Treat failures as advisory until
      proven stable. See `CLAUDE.md` § Automation Goals #4.

---

## Developer Experience

- [ ] 📋 **Wayland headless investigation** — GNOME >= 45 is increasingly
      Wayland-native. Explore whether `xvfb-run` can be replaced or supplemented
      with a Wayland headless compositor (e.g. `weston --headless` or
      `gnome-shell --wayland --headless`) for the `install.yml` and `smoke.yml`
      tests. Document findings; only switch if it's demonstrably more reliable.

---

## Completed

_Move items here (with version/date) when the corresponding PR merges._

- [x] 📋 Initial `CLAUDE.md` project instructions document
- [x] 📋 `docs/repo-fork-workflow.md` — branch strategy documentation
- [x] 📋 `.claude/skills/` — commit-message, pr-description, changelog,
      gnome-extension-release, node-version-sync skills
- [x] 📋 `CHANGELOG.md` — initial changelog seeded from upstream v25
- [x] 📋 `.devcontainer/` — devcontainer with Node 23 + gnome-shell + xvfb
- [x] 📋 `.github/ISSUE_TEMPLATE/` — bug_report, gnome_version, test_failure,
      ego_submission templates
- [x] 📋 `.github/pull_request_template.md` — default PR body template
- [x] 📋 `.github/labels.yml` + `sync-labels.yml` — declarative label management
      with automated sync on push to `graham/main`
- [x] 👤 Branch protection on `graham/main` — required checks (install, lint),
      required PR review, auto-merge enabled
- [x] 👤 GitHub Actions permissions — write access for contents, issues,
      and pull-requests granted via workflow permissions setting
- [x] 📋 `CONTRIBUTING.md` — contributor guide covering branch workflow, commit
      conventions, PR process, and release pipeline overview
- [x] 📋 `CODE_OF_CONDUCT.md` — Contributor Covenant v2.1
- [x] 📋 `.github/DISCUSSION_TEMPLATE/` — q-and-a, ideas, show-and-tell,
      compatibility discussion category templates
- [x] 🤖 `.github/dependabot.yml` — Dependabot for npm packages (ESLint toolchain)
      and GitHub Actions, both with 14-day cooldown for supply-chain safety;
      supersedes the planned `dependency-update.yml` custom workflow
- [x] 📋 **`README.md` update** — the upstream README covers installation only.
      Add a "Maintainer Notes" or "Fork Notes" section explaining this is a maintained
      fork, pointing to the EGO listing, and noting the automated release pipeline.
      Keep it brief — the detailed docs live in `docs/` and `CLAUDE.md`.
