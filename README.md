# Reorder Workspaces

A [GNOME Shell](https://wiki.gnome.org/Projects/GnomeShell)
[extension](https://wiki.gnome.org/Projects/GnomeShell/Extensions) to reorder
[workspaces](https://help.gnome.org/users/gnome-help/stable/shell-workspaces)
with `Ctrl`+`Super`+`Up`/`Down`.

## Attribution

This extension was originally created by [smmr0](https://github.com/smmr0) and published
at [smmr0/gnome-reorder-workspaces](https://github.com/smmr0/gnome-reorder-workspaces). The
original work — the extension logic, preferences UI, keybinding system, and GSettings schema —
is entirely theirs, and this project would not exist without it.

This is a maintained fork. The upstream extension has not seen active maintenance in some time,
with open issues going unaddressed. This fork exists to keep the extension working with current and future GNOME Shell versions, and to
automate the release lifecycle so that compatibility updates happen promptly when new GNOME Shell
versions are released.

If the upstream project becomes active again, this fork will defer to it.

## Installation

### [EGO](https://extensions.gnome.org/) (recommended)

<https://extensions.gnome.org/extension/3685/reorder-workspaces/>

This listing is shared with the upstream extension. The version published here may lag a few
days behind a GitHub Release while awaiting EGO review.

### Manual (from source)

Prerequisites: Node.js (version pinned in [`.node-version`](.node-version)), `gnome-extensions`
CLI (part of the `gnome-shell` package on most distributions).

```bash
corepack enable
yarn install
yarn run gnome-install
```

`yarn run gnome-install` builds the extension, installs it for the current user, and enables it.
A GNOME Shell restart is required on X11 (`Alt`+`F2` → `r` → `Enter`); on Wayland, log out and
back in.

### Prebuilt zip

Download the zip from the [latest GitHub Release](https://github.com/GingerGraham/gnome-reorder-workspaces/releases/latest)
and install it manually:

```bash
gnome-extensions install reorder-workspaces@jer.dev.shell-extension.zip
gnome-extensions enable reorder-workspaces@jer.dev
```

## Supported GNOME Shell Versions

See the `shell-version` array in [`src/metadata.json`](src/metadata.json) for the current list.
New GNOME Shell versions are detected automatically and a compatibility PR is opened within a
week of a new release tag appearing upstream.

## Configuration

Open the extension preferences via GNOME Extensions app or:

```bash
gnome-extensions prefs reorder-workspaces@jer.dev
```

**Keybindings** — the default `Ctrl`+`Super`+`Up`/`Down` can be changed to any accelerator.

**Behaviour outside the Overview** — controls what the keybindings do when the GNOME Overview
is not open:

| Setting                 | Effect                                                                          |
| ----------------------- | ------------------------------------------------------------------------------- |
| **Reorder** _(default)_ | Keybindings reorder workspaces from anywhere                                    |
| **Default**             | Keybindings behave normally outside the overview (may cause animation glitches) |
| **Disabled**            | Keybindings have no effect outside the overview                                 |

## Maintainer Notes

This fork uses an automated release pipeline:

- New GNOME Shell versions are detected weekly via the GNOME GitLab API and a PR is opened
  automatically to add support.
- Merging a version bump PR triggers a GitHub Release with the built zip attached.
- EGO submission is manual — a tracking issue is opened as a reminder after each release.

See [`CLAUDE.md`](CLAUDE.md) for the full automation design and [`CHANGELOG.md`](CHANGELOG.md)
for the version history.

## AI Tooling

This fork is maintained with significant AI assistance and I want to be transparent about that:

- **[Claude](https://claude.ai)** (Anthropic) — used heavily throughout: workflow design, code
  review, documentation, commit messages, and the automation architecture described in
  [`CLAUDE.md`](CLAUDE.md).
- **[GitHub Copilot](https://github.com/features/copilot)** — used for in-editor code
  suggestions, commit messages, and GitHub-integrated reviews (PR summaries, code review comments).

The extension logic itself originates with smmr0. My contribution is primarily the maintenance
infrastructure and keeping compatibility current — and AI tooling is a core part of how I do
that efficiently as a solo maintainer.

If you have strong views on AI-assisted open source, that's a fair conversation to have in
[Discussions](https://github.com/GingerGraham/gnome-reorder-workspaces/discussions).

## License

[GNU General Public License v3.0](LICENSE) — same as the upstream extension.
