# Argentazul - Fedora Atomic Update Script

## Overview

The `argentazul` script automates the process of updating Fedora atomic
distributions using standard container infrastructure, allowing
customization with standard `Containerfile` commands.

## Features

- Operates on either your host system or `toolbox` containers.
- Checks for and deploys latest updates easily.
- Aggressively reclaims disk space from stale containers/images

## Requirements

- An atomic Fedora distribution such as Silverblue or Kinoite

## Usage

```bash
./argentazul [-f|--force] [-c|--config FILE] [--distro DISTRO] [--release RELEASE] [--toolbox TOOLBOX] [purge|update]
```

### Commands

- `update` (default): Builds and deploys updates if available.
- `purge`: Cleans intermediate containers and images.  Also done automatically
  after pulling updates from the container registry.

### Options

- `-f, --force`: Forces a build and deployment even if no updates are
  available.
- `-c FILE, --config FILE`: Specifies an additional configuration file.
- `--distro DISTRO`: Overrides the distribution variant (e.g., `silverblue`).
- `--release RELEASE`: Overrides the release version (e.g., `42`).
- `--toolbox TOOLBOX`: Build and deploy the toolbox container of the given name"
  instead of the host system. An argument of `default` specifies the default toolbox
  container.
- `-h, --help`: Displays help and exits.

## Environment and Configuration Variables

The script supports the following variables, which can be set in the
environment or in configuration files:
- `REGISTRY`: Container registry (default: `quay.io/fedora-ostree-desktops`).
- `DISTRO`: Distribution variant (auto-detected from `/usr/lib/os-release` if
  unset).
- `RELEASE`: Release version (auto-detected from `/usr/lib/os-release` if
  unset).
- `FORCE`: Forces image build and deployment (default: `false`).
- `CONFIGDIR`: Directory for configuration files (auto-detected via XDG paths).
- `CONTAINERFILE`: Path to a custom Containerfile (auto-detected via XDG
  paths).

## Configuration Files

The script searches for configuration files in XDG paths (e.g.
`$XDG_CONFIG_HOME/argentazul`, usually `~/.config/argentazul`). It looks for:
- `config`: A shell script to set variables.
- `Containerfile`: A partial `Containerfile` for customizing the image.
  An appropriate `FROM` directive and the following `ARG` directives are
  automatically prepended:
    * `REGISTRY`: The registry being used (e.g. `quay.io/fedora-ostree-desktops`)
    * `DISTRO`: The distribution variant (`silverblue`, `kinoite`, ...)
    * `RELEASE`: The distribution version (e.g. `42`)
    * `FROM`: The source container (e.g. `quay.io/fedora-ostree-desktops/silverblue:42`)
    * `HOSTNAME`: The host system's hostname
    * `TOOLBOX`: The name of the toolbox container being built (if applicable)

When running with `--toolbox`, files are looked for in a subdirectory with
the specified name, e.g. `~/.config/argentazul/default/Containerfile` for
`--toolbox default`.

## Example
To update a system (detecting distribution and release):
```bash
./argentazul
```

To force an update with a custom configuration:
```bash
./argentazul -f -c ./custom-config.sh
```

To purge stale containers and images:
```bash
./argentazul purge
```

## License
[MIT](License)
