# Patches carried on versaterm/main

Every patch listed here is meant to go upstream. Remove a row (and the commits) once the
upstream release that `versaterm/main` is based on contains the change.

Base: upstream `v0.9.12`

| Patch | Upstream issue | Upstream PR | Status | Commits |
| --- | --- | --- | --- | --- |
| _none yet_ | | | | |

Fork infrastructure commits (`VERSATERM.md`, `PATCHES.md`, `docker/versaterm/`,
`.github/workflows/versaterm-*.yml`, `.github/CODEOWNERS`) are not patches and are never
proposed upstream.

## Upstream contribution candidates (no patch carried yet)

- Upstream CI breaks on the current `ubuntu-latest` runner: the bundled hiredis
  `CMakeLists.txt` is rejected by CMake 4, and `v0.9.12` does not build with Boost 1.88
  (upstream `main` fixes Boost with #454).
