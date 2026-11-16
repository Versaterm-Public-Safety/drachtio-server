# Patches carried on versaterm/main

Every patch listed here is meant to go upstream. Remove a row (and the commits) once the
upstream release that `versaterm/main` is based on contains the change.

Base: upstream `v0.9.12`

| Patch | Upstream issue | Upstream PR | Status | Commits |
| --- | --- | --- | --- | --- |
| Discard a 2xx retransmitted over a reliable transport before the ACK is sent | [#513](https://github.com/drachtio/drachtio-server/issues/513) | [#515](https://github.com/drachtio/drachtio-server/pull/515) | carried, upstream PR open | 59aa481e83 |
| Do not force requests onto a closed connection cached for a registration | [#512](https://github.com/drachtio/drachtio-server/issues/512) | [#516](https://github.com/drachtio/drachtio-server/pull/516) | carried, upstream PR open | 450fa40a08 |

Fork infrastructure commits (`VERSATERM.md`, `PATCHES.md`, `docker/versaterm/`,
`.github/workflows/versaterm-*.yml`, `.github/CODEOWNERS`) are not patches and are never
proposed upstream.

## Upstream contribution candidates (no patch carried yet)

- Upstream CI breaks on the current `ubuntu-latest` runner: the bundled hiredis
  `CMakeLists.txt` is rejected by CMake 4, and `v0.9.12` does not build with Boost 1.88
  (upstream `main` fixes Boost with #454).
