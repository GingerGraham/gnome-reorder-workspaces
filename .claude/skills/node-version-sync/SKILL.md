---
name: node-version-sync
description: >
  Keep the Node.js version consistent across all places it is declared in this repo.
  Use this skill whenever the user asks to update Node, bump the Node version, or
  when any workflow step involves changing .node-version or the devcontainer image.
  Also use it when reviewing a PR that touches either file to verify they are in sync.
---

# Node Version Sync Skill

Node is declared in two places in this repo. They must always match. Letting them
drift causes silent test-vs-dev environment mismatches.

---

## Source of Truth

`.node-version` is the single source of truth. Everything else derives from it.

| File | Field | Example value |
|------|-------|---------------|
| `.node-version` | entire file content | `23.11.0` |
| `.devcontainer/Dockerfile` | base image tag (major only) | `mcr.microsoft.com/devcontainers/javascript-node:1-23` |

The devcontainer image uses only the **major version** (e.g. `23`), not the full
semver. The `javascript-node` devcontainer image resolves to the latest patch
within that major at build time, which is intentional — patch updates in the image
are automatic. Only the major needs to change when `.node-version` bumps a major.

The `package.json` `packageManager` field (`yarn@4.9.1`) is independent of Node
and does not need to change on a Node version bump.

---

## When to Update

Update Node when:
- A new Node.js **LTS** version is released and has been available for at least one month
  (gives the `javascript-node` devcontainer image time to stabilise)
- The current version in `.node-version` reaches end-of-life
  (check: https://endoflife.date/nodejs)
- A Node security advisory affects the version in use

Do **not** chase every minor/patch. The devcontainer image handles patch updates
automatically within the pinned major.

---

## How to Update

### 1. Determine the new version

Check the current LTS schedule at https://nodejs.org/en/about/releases/ or
https://endoflife.date/nodejs. Pick the latest LTS major unless there is a
specific reason to use a non-LTS version (there isn't for this project).

### 2. Update `.node-version`

Set the full semver of the latest stable release in that LTS line:

```
23.11.0   →   24.x.y
```

Find the exact latest patch at https://nodejs.org/en/download/ or via:
```bash
# List available versions (requires Node installed locally or in container)
curl -s https://nodejs.org/dist/index.json | jq '[.[] | select(.lts != false)] | .[0]'
```

### 3. Update `.devcontainer/Dockerfile`

Change only the major in the base image tag:

```dockerfile
# Before
FROM mcr.microsoft.com/devcontainers/javascript-node:1-23

# After (example: bumping to Node 24)
FROM mcr.microsoft.com/devcontainers/javascript-node:1-24
```

Verify the target tag exists before changing it:
```bash
# Check available tags (requires skopeo, or just check Docker Hub)
skopeo list-tags docker://mcr.microsoft.com/devcontainers/javascript-node \
  | jq '.Tags[] | select(startswith("1-"))'
# Or browse: https://mcr.microsoft.com/en-us/artifact/mar/devcontainers/javascript-node/tags
```

### 4. Verify nothing else needs updating

Run this grep to catch any other hardcoded Node references:
```bash
grep -rn 'node:' .github/workflows/
grep -rn 'node-version' .github/workflows/
```

The CI workflows use `node-version-file: '.node-version'` (not a hardcoded version),
so they pick up the change automatically — no workflow edits needed.

### 5. Test the change

Inside the devcontainer after rebuilding (`Dev Containers: Rebuild Container`):
```bash
node --version   # should match .node-version major
yarn --version   # should still resolve correctly via corepack
yarn run lint
yarn run build
```

---

## Commit Message

Follow the repo's Conventional Commits convention:

```
chore(deps): bump Node.js from 23 to 24

Updates .node-version to 24.x.y and devcontainer base image to
javascript-node:1-24.

Node 23 reaches end-of-life YYYY-MM-DD. Node 24 is the current LTS.
```

---

## PR Checklist

- [ ] `.node-version` updated to full semver of new version
- [ ] `.devcontainer/Dockerfile` base image tag major updated
- [ ] `grep` confirms no other hardcoded Node versions in workflows
- [ ] Devcontainer rebuilt and `yarn run lint` + `yarn run build` pass
- [ ] `install.yml` CI passes on the PR (uses `.node-version` automatically)
- [ ] `CHANGELOG.md` updated under `[Unreleased]` → `Changed`

---

## What Not to Do

- Don't pin the devcontainer to a full semver tag (e.g. `1-23.11.0`) — those tags
  may not exist and you lose automatic patch updates
- Don't update `.node-version` without updating the Dockerfile, or vice versa
- Don't use non-LTS Node versions unless there's a documented reason
- Don't bump Node and bump GNOME shell-version in the same PR — keep changes
  independently reviewable and revertable