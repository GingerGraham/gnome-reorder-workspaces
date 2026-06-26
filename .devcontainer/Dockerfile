# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/devcontainers/javascript-node:1-23

# Install gnome-shell tooling (for gnome-extensions CLI) and xvfb (for install tests)
# These match what the CI install.yml workflow installs via apt
RUN apt-get update && apt-get install -y --no-install-recommends \
	gnome-shell \
	xvfb \
	unzip \
	&& rm -rf /var/lib/apt/lists/*

# Enable corepack so yarn is available at the correct version
# The actual `corepack enable` + `yarn install` runs in postCreateCommand
# so it picks up the pinned version from package.json at workspace-mount time
RUN corepack enable
