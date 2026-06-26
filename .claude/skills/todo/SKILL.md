---
name: todo
description: >
  Maintain TODO.md for the gnome-reorder-workspaces repo. Use this skill whenever
  the user asks to mark a task complete, add a new task, or update the backlog.
  Also use it when a PR merges and the corresponding TODO item needs closing, or
  when a new piece of work is identified during any other task and should be
  tracked. Covers both the mechanical edit process and the conventions for writing
  good task entries.
---

# TODO Skill

`TODO.md` lives at the repo root and is the single backlog for this project.
It is not a substitute for GitHub Issues — it tracks *planned* work before it
becomes an actionable Issue or PR. Once work is in-flight (PR open), the PR
itself is the source of truth; the TODO item stays open until the PR merges.

---

## File Structure

```
# TODO

<preamble — do not edit>

---

## <Section>

- [ ] <emoji> **<title>** — <description>

---

## Completed

- [x] <emoji> <title> — <brief note> (v<N> / <YYYY-MM-DD>)
```

### Sections (in order)

| Section | What belongs here |
|---|---|
| `GitHub Repository Setup` | One-time repo config: templates, labels, branch protection, permissions |
| `CI/CD Workflows` | GitHub Actions workflows to create or significantly change |
| `Developer Experience` | Tooling, devcontainer, skills, local workflow improvements |
| `Documentation` | README, CONTRIBUTING, docs/, CLAUDE.md changes |
| `Completed` | Done items, moved from above sections |

If a new item doesn't fit an existing section, add a new `## Section` before
`## Completed`. Keep sections ordered: repo setup → automation → DX → docs →
done.

### Emoji legend

| Emoji | Meaning |
|---|---|
| 🤖 | Fully automatable — can be implemented entirely in CI/workflows/scripts |
| 👤 | Requires human action — config, EGO submission, account access, etc. |
| 📋 | Meta/process — skills, templates, documentation, repo setup |

Every item must have exactly one emoji prefix.

---

## Completing a Task

When a PR merges that completes a TODO item:

**1. Read `TODO.md` first** — find the exact item text before editing.

**2. Remove the item from its current section** — delete the entire bullet
(including any sub-bullets that are part of the same item).

**3. Add a one-line summary to `## Completed`** — condensed to a single line,
past tense, with version or date in parentheses:

```markdown
- [x] 🤖 `detect-gnome-release.yml` — automated GNOME version bump PRs (v26 / 2025-07-01)
```

Use the extension `version` from `src/metadata.json` if the work ships as part
of a release. Use the date (`YYYY-MM-DD`) for infrastructure/tooling work that
doesn't correspond to an extension version bump.

**4. Update `CHANGELOG.md`** — add the appropriate entry under `[Unreleased]`
per the changelog skill. The TODO completion and the CHANGELOG entry should be
in the same commit.

**5. Commit message:**

```
docs: mark <item title> complete in TODO

Corresponding PR: #<N>
```

---

## Adding a New Task

**1. Read `TODO.md` first** — check it isn't already tracked (possibly under a
different name).

**2. Choose the right section** — use the table above. If genuinely uncertain,
put it in the most specific section that fits.

**3. Write the entry:**

```markdown
- [ ] <emoji> **`<filename>`** or **<Short Title>** — <one or two sentence description>.
  Include: what it does, why it's needed, any key constraints or dependencies.
  Reference related skills or files where helpful.
```

Rules:
- Title in bold. If it's a file/workflow, use backtick-bold: **`filename.yml`**
- Description starts after the em dash `—`
- Keep to 1–3 lines. Link to a skill or section of `CLAUDE.md` for detail rather
  than expanding inline
- If the item depends on another TODO item being done first, note it:
  `Depends on: GitHub Actions permissions item above`
- Place the new item at the bottom of its section unless there's a clear reason
  to prioritise it higher

**4. Commit message:**

```
docs: add <item title> to TODO
```

---

## Editing an Existing Task

If requirements change for a not-yet-started item, edit in place — don't remove
and re-add. If the change is significant, note it with a brief inline comment:

```markdown
- [ ] 🤖 **`dependency-update.yml`** — scheduled Yarn dependency updates (monthly).
  ~~Alternative: Dependabot~~ Using Dependabot instead — see `.github/dependabot.yml` item.
```

For in-flight work (PR open), don't edit the TODO — update the PR description instead.

---

## What NOT to Track Here

- Individual bug reports → open a GitHub Issue
- Questions or investigations without a clear deliverable → not tracked until
  the deliverable is defined
- Upstream changes to `smmr0/gnome-reorder-workspaces` → tracked by syncing
  upstream, not in TODO
- EGO submission for a specific release → tracked by the auto-opened GitHub Issue
  from `release.yml`, not in TODO