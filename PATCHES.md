# Patches carried on versaterm/main

Every patch listed here is meant to go upstream. Remove a row (and the commits) once the
upstream release that `versaterm/main` is based on contains the change.

Base: upstream `v0.9.12`

| Patch | Upstream issue | Upstream PR | Status | Commits |
| --- | --- | --- | --- | --- |
| Discard a 2xx retransmitted over a reliable transport before the ACK is sent | [#513](https://github.com/drachtio/drachtio-server/issues/513) | [#515](https://github.com/drachtio/drachtio-server/pull/515) | carried, merged upstream (main, not yet released) | 59aa481e83 |
| Do not force requests onto a closed connection cached for a registration | [#512](https://github.com/drachtio/drachtio-server/issues/512) | [#516](https://github.com/drachtio/drachtio-server/pull/516) | carried, merged upstream (main, not yet released) | 450fa40a08 |
| Test only: remove a race in the out-of-dialog REFER sipp scenario that made the fixture fail intermittently | — | [#517](https://github.com/drachtio/drachtio-server/pull/517) | carried, merged upstream (main, not yet released) | 82e456d722 |
| Build only: let bundled hiredis and prometheus-cpp configure under CMake 4 | — | [#518](https://github.com/drachtio/drachtio-server/pull/518) (our [#519](https://github.com/drachtio/drachtio-server/pull/519) was superseded by it and closed) | carried, merged upstream (main, not yet released) | d64ae3c28a, then cherry-pick of upstream cd1a1d03b5 |
| Backport: adapt to Boost 1.88 (builds on current `ubuntu-latest`) | — | [#454](https://github.com/drachtio/drachtio-server/pull/454) | carried, merged upstream (main, not yet released) | 479eb974ef |

All carried patches are now in upstream `main`: on the next upstream release, rebase
`versaterm/main` onto it and drop every row above.

Fork infrastructure commits (`VERSATERM.md`, `PATCHES.md`, `docker/versaterm/`,
`.github/workflows/versaterm-*.yml`, `.github/CODEOWNERS`) are not patches and are never
proposed upstream.

## Upstream contribution candidates (no patch carried yet)

- None at the moment.