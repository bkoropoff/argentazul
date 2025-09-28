# Argentazul - Fedora Atomic Update Script

## Overview

The `argentazul` script automates the process of updating Fedora atomic
distributions using standard container infrastructure, allowing
customization with standard `Containerfile` commands.

## Features

- Check for and deploy latest updates
- Customize image with `Containerfile` commands
- Builds and deploys images using `buildah` and `rpm-ostree`.
- Maintains state to track updates and avoid unnecessary rebuilds.

## Requirements

- An atomic Fedora distribution such as Silverblue or Kinoite

## Usage

```bash
./argentazul [-f|--force] [-c|--config FILE] [--distro DISTRO] [--release RELEASE] [purge|update]
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
- `OUTDIR`: Output and working directory for images (default:
  `/var/cache/argentazul`).

## Configuration Files

The script searches for configuration files in XDG paths (e.g.
`$XDG_CONFIG_HOME/argentazul`, usually `~/.config/argentazul`). It looks for:
- `config`: A shell script to set variables.
- `Containerfile`: A partial Containerfile for customizing the image. `FROM`
  and `REGISTRY` directives as well as the final `RUN ostree container
  commit` are automatically added.  The following arguments are available:
    * `DISTRO`: The distribution variant (`silverblue`, `kinoite`, ...)
    * `RELEASE`: The distribution version (e.g. `42`)

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

## Notes

- The script requires write access to `$OUTDIR` (default:
  `/var/cache/argentazul`), as `rpm-ostree` can't read images from your home
  directory. If the directory doesn't exist, it attempts to create it with
  `sudo` and set it writable by the `wheel` group.

## License
[MIT](License)
