# blank-devbox

Your new project canvas: a blank, batteries-included dev environment powered by
[Devbox](https://www.jetify.com/devbox) inside a [Dev Container](https://containers.dev/).

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/prodbytes/blank-devbox)
[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=007ACC&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/prodbytes/blank-devbox)

## What's inside

Toolchain pinned by [devbox.json](devbox.json) and locked in [devbox.lock](devbox.lock):

| Tool | Version |
|------|---------|
| GraalVM CE (musl) | 25.0.2 |
| Node.js | 26.x |
| Python | 3.14.x |
| PostgreSQL | 17.x |

## Getting started

Click a badge above, or locally:

```bash
git clone git@github.com:prodbytes/blank-devbox.git
code blank-devbox   # then "Reopen in Container" when prompted
```

Once inside the container:

```bash
devbox shell        # enter the environment
devbox run node --version
devbox add go@1.24  # add more tools (updates devbox.json + devbox.lock)
```

## How the container is built

The [Containerfile](.devcontainer/Containerfile) keeps the Microsoft
`ubuntu-24.04` devcontainer base image and layers Devbox on top:

1. Devbox is installed as root, then everything else runs as the `vscode` user
   so the Nix store ownership matches the container's `remoteUser`.
2. Nix is installed in single-user mode (`--no-daemon`) — containers have no
   systemd, so the multi-user Nix daemon can't run.
3. At build time the locked store paths are fetched straight from
   `cache.nixos.org` to warm `/nix/store` (no GitHub API calls, so builds
   don't hit unauthenticated rate limits).
4. On container start, `postCreateCommand` runs `devbox install`, which
   re-links against the warm store and finishes in seconds.
