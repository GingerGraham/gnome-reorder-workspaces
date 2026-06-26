# TODO

Planned work for the `gnome-reorder-workspaces` maintained fork. Ordered roughly
by priority within each section. Check items off as PRs merge; move completed
items to [`CHANGELOG.md`](CHANGELOG.md) under the appropriate version.

Items marked 🤖 are fully automatable. Items marked 👤 require human action.
Items marked 📋 are meta/process work (skills, templates, docs).

---

## GitHub Repository Setup

These are one-time setup tasks that make everything else work properly.

- [ ] 📋 **GitHub Issue templates** — create `.github/ISSUE_TEMPLATE/` with templates for:
  - `bug_report.yml` — extension not working, crash, unexpected behaviour
  - `gnome_version.yml` — request to add support for a new GNOME Shell version
  - `test_failure.yml` — auto-opened by `test.yml` on unit test failure (pre-filled
    with logs; label: `test-failure`)
  - `ego_submission.yml` — tracking issue for EGO submission after a GitHub Release
    (auto-opened by `release.yml`; label: `ego-submission`)

- [ ] 📋 **GitHub PR template** — create `.github/pull_request_template.md` as the
  default PR body (see `.claude/skills/pr-description/SKILL.md` for per-category
  templates; this is the fallback)

- [ ] 📋 **GitHub Labels** — create labels referenced in skills and workflows:
  `automated`, `version-bump`, `breaking-change`, `bug`, `enhancement`, `ci`,
  `documentation`, `dependencies`, `test-failure`, `ego-submission`
  Consider using a `gh label` script or `.github/labels.yml` + action to keep
  labels declarative and reproducible across forks.

- [ ] 📋 **Branch protection rules** on `graham/main`:
  - Require status checks: `install` (from `install.yml`), `lint` (from `lint.yml`),
    `test` (from `test.yml` once created)
  - Require PR before merging
  - Allow auto-merge for PRs where all checks pass (enables the automated version
    bump flow to merge without manual intervention)

- [ ] 📋 **GitHub Actions permissions** — confirm the `GITHUB_TOKEN` has write
  access to contents (for creating releases), issues (for opening tracking issues),
  and pull-requests (for opening version bump PRs). Set in repo Settings →
  Actions → General → Workflow permissions.

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

- [ ] 🤖 **`dependency-update.yml`** — scheduled Yarn dependency updates (monthly),
  open a PR with updated `yarn.lock`. Low risk as deps are dev-only (ESLint).
  Alternative: enable Dependabot for npm ecosystem instead of a custom workflow.

---

## Developer Experience

- [ ] 📋 **Dependabot config** (`.github/dependabot.yml`) — keep GitHub Actions
  action versions current (e.g. `actions/checkout`, `actions/setup-node`).
  Ecosystem: `github-actions`. Weekly schedule. Auto-assign label `dependencies`.

- [ ] 📋 **`.claude/skills/node-version-sync/SKILL.md`** — already drafted in
  this session; commit to repo ✓

- [ ] 📋 **Wayland headless investigation** — GNOME >= 45 is increasingly
  Wayland-native. Explore whether `xvfb-run` can be replaced or supplemented
  with a Wayland headless compositor (e.g. `weston --headless` or
  `gnome-shell --wayland --headless`) for the `install.yml` and `smoke.yml`
  tests. Document findings; only switch if it's demonstrably more reliable.

---

## Documentation

- [ ] 📋 **`CONTRIBUTING.md`** — contributor guide covering: branch workflow,
  commit message conventions (Conventional Commits), PR process, how the
  automated release pipeline works, and how to test locally. Can pull heavily
  from existing skills and `docs/repo-fork-workflow.md`.

- [ ] 📋 **`README.md` update** — the upstream README covers installation only.
  Add a "Maintainer Notes" or "Fork Notes" section explaining this is a maintained
  fork, pointing to the EGO listing, and noting the automated release pipeline.
  Keep it brief — the detailed docs live in `docs/` and `CLAUDE.md`.

---

## Completed

_Move items here (with version/date) when the corresponding PR merges._

- [x] 📋 Initial `CLAUDE.md` project instructions document
- [x] 📋 `docs/repo-fork-workflow.md` — branch strategy documentation
- [x] 📋 `.claude/skills/` — commit-message, pr-description, changelog,
  gnome-extension-release, node-version-sync skills
- [x] 📋 `CHANGELOG.md` — initial changelog seeded from upstream v25
- [x] 📋 `.devcontainer/` — devcontainer with Node 23 + gnome-shell + xvfb