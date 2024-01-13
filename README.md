# repro-sources-list.sh

[`repro-sources-list.sh`](./repro-sources-list.sh) configures `/etc/apt/sources.list` and similar files
for installing packages from a snapshot to help [Reproducible Builds](https://reproducible-builds.org/).

```dockerfile
# SOURCE_DATE_EPOCH is set to 1691114774 (i.e., 20230804T020614Z, timestamp of /etc/apt/sources.list)
FROM ubuntu:jammy-20230804
ENV DEBIAN_FRONTEND=noninteractive
RUN \
  --mount=type=cache,target=/var/cache/apt,sharing=locked \
  --mount=type=cache,target=/var/lib/apt,sharing=locked \
  --mount=type=bind,source=./repro-sources-list.sh,target=/usr/local/bin/repro-sources-list.sh \
  repro-sources-list.sh && \
  apt-get update && \
  apt-get install -y gcc
```

Examples:
- [`Dockerfile.debian-11`](./Dockerfile.debian-11)
- [`Dockerfile.debian-12`](./Dockerfile.debian-12)
- [`Dockerfile.ubuntu-2204`](./Dockerfile.ubuntu-2204)
- [`Dockerfile.ubuntu-2304`](./Dockerfile.ubuntu-2304)
- [`Dockerfile.archlinux`](./Dockerfile.archlinux)

## Hints
- To preserve the package cache on GHA, use <https://github.com/reproducible-containers/buildkit-cache-dance>.
  See [`.github/workflows/main.yaml`](./.github/workflows/main.yaml) for an example.

- For Debian >= 13 and Ubuntu >= 23.10, see also [`./alternative/`](./alternative/)
  to lock packages without using [`repro-sources-list.sh`](./repro-sources-list.sh).

### Environment variables

| Variable                  | Description                                   | Default value                                                      |
|---------------------------|-----------------------------------------------|--------------------------------------------------------------------|
| `SOURCE_DATE_EPOCH`       | Timestamp of the snapshot (int64)             | Timestamp of `/etc/apt/sources.list`, etc. (See below)             |
| `WRITE_SOURCE_DATE_EPOCH` | Write the `SOURCE_DATE_EPOCH` value to a file | `/dev/null`                                                        |
| `SNAPSHOT_ARCHIVE_BASE`   | Base URL of the snapshot                      | `http://snapshot-cloudflare.debian.org/archive/`, etc. (See below) |
| `BACKPORTS`               | Enable Debian backports                       | `0`                                                                |
| `KEEP_CACHE`              | Keep apt cache                                | `1`                                                                |

Distribution-specific default values:

| Distribution   | `SOURCE_DATE_EPOCH`                                   | `SNAPSHOT_ARCHIVE_BASE`                          |
|----------------|-------------------------------------------------------|--------------------------------------------------|
| Debian (<= 11) | Timestamp of `/etc/apt/sources.list`                  | `http://snapshot-cloudflare.debian.org/archive/` |
| Debian (>= 12) | Timestamp of `/etc/apt/sources.list.d/debian.sources` | `http://snapshot-cloudflare.debian.org/archive/` |
| Ubuntu         | Timestamp of `/etc/apt/sources.list`                  | `http://snapshot.ubuntu.com/`                    |
| ArchLinux      | Timestamp of `/var/log/pacman.log`                    | `http://archive.archlinux.org/`                  |


## Related project
<https://github.com/reproducible-containers/repro-pkg-cache> contains Dockerfile examples to reproduce package cache
with specific versions, by pushing the cache to an image registry.

|Project                                                           |Cache location                           |Best for                             |
|------------------------------------------------------------------|-----------------------------------------|-------------------------------------|
|<https://github.com/reproducible-containers/repro-sources-list.sh>|Distros' permanent snapshot servers (\*1)|Debian, Ubuntu, ArchLinux            |
|<https://github.com/reproducible-containers/repro-pkg-cache>      |Your own permanent image registry        |Alpine, Fedora, Rocky, openSUSE, etc.|

(\*1): The packages can be also ephemerally cached on GitHub Actions to reduce loads on distros' snapshot servers.
See the [Hints](#hints) above.
