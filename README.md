# repro-sources-list.sh

[`repro-sources-list.sh`](./repro-sources-list.sh) configures `/etc/apt/sources.list` and similar files for installing packages from a snapshot.

Examples:
- [`Dockerfile.debian-11`](./Dockerfile.debian-11)
- [`Dockerfile.debian-12`](./Dockerfile.debian-12)
- [`Dockerfile.archlinux`](./Dockerfile.archlinux)

## Hint
- To preserve the package cache on GHA, use <https://github.com/overmindtech/buildkit-cache-dance>.
  See [`.github/workflows/main.yaml`](./.github/workflows/main.yaml) for an example.
