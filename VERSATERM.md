# Versaterm fork of drachtio-server

This repository is [Versaterm](https://www.versaterm.com)'s fork of
[drachtio/drachtio-server](https://github.com/drachtio/drachtio-server).

It exists for two reasons:

1. **Contribute upstream.** Fixes and features that Versaterm develops for drachtio-server
   are proposed to the upstream project as focused, self-contained pull requests.
2. **Ship patches early.** While a contribution is under review upstream, Versaterm builds
   and uses an image from `versaterm/main`, which is an upstream release plus the pending patches.

The goal is to carry as few patches as possible, for as short a time as possible. Every
patch in this fork is meant to go upstream. Each one is dropped once upstream releases it.

## Branches

| Branch | Content | Rules |
| --- | --- | --- |
| `main` | Strict mirror of `upstream/main` | Never commit here. Only the sync workflow fast-forwards it. |
| `fix/<upstream-issue>-<slug>`, `feat/<slug>` | One upstream contribution each | Branch from `upstream/main`. One topic. Opened as a PR against `drachtio/drachtio-server:main`. |
| `versaterm/main` (default) | Upstream release + fork infrastructure + pending patches | Changes through PRs. Rebased by a maintainer on each new upstream release. |

Release tags on `versaterm/main` are `vX.Y.Z-vt.N`. `vX.Y.Z` is the upstream release used
as the base, and `N` increments with each Versaterm build on that base.

```text
upstream/main ──●──●──●──●  (vX.Y.Z)
                         ├── fix/512-...   ──► PR to drachtio/drachtio-server
                         ├── fix/513-...   ──► PR to drachtio/drachtio-server
                         └── versaterm/main = vX.Y.Z + fork infra + cherry-picked fixes ──► vX.Y.Z-vt.N image
```

## Contributing a fix or feature

1. **Upstream issue first.** Open or find an issue on
   [drachtio/drachtio-server](https://github.com/drachtio/drachtio-server/issues). Describe the
   behaviour in protocol terms and say that a PR is coming, so nobody duplicates the work.
2. **Branch from upstream.** `git fetch upstream && git switch -c fix/<issue>-<slug> upstream/main`.
   The branch must not depend on any other fork patch or on fork infrastructure.
3. **Write the regression test first.** Use the upstream test harness (`test/`): tape plus
   `drachtio-srf`, with SIPp scenarios under `test/scenarios/` and a drachtio config
   `test/drachtio.conf*.xml` when needed. The test must **fail without the fix**. Add new test
   files to `npm run test-ci` in `test/package.json`.
4. **Fix with a minimal diff.** Follow the surrounding code style. Do not reformat unrelated
   code or bump dependencies in the same PR. If the fix belongs in sofia-sip (`deps/sofia-sip`),
   send it to that project instead.
5. **Run the upstream test suite.** Pushing the branch runs `Versaterm CI`, which runs the
   upstream build and `npm run test-ci` on a pinned runner, and upstream's own `CI` workflow
   on this fork. See [CI](#ci) below.
6. **Open the PR against `drachtio/drachtio-server:main`.** Follow the PR checklist below.
7. **Carry it in the fork if needed.** Cherry-pick the commits onto `versaterm/main` through a PR,
   and add a row to [PATCHES.md](PATCHES.md).

### Upstream PR checklist

Model: [drachtio/drachtio-server#510](https://github.com/drachtio/drachtio-server/pull/510).

- [ ] The title says what changes in behaviour, e.g. `fix: ...` / `feat: ...`.
- [ ] The problem is described, with a SIP sequence diagram when a message exchange is involved.
- [ ] Protocol references are cited where relevant (RFC 3261 section, etc.).
- [ ] The description explains why the fix is correct and covers edge cases, including what
      deliberately does not change.
- [ ] Regression tests use the existing harness, are wired into `test-ci`, and fail without
      the fix.
- [ ] Upstream CI is green.
- [ ] One topic per PR, with no unrelated changes.
- [ ] **Nothing product-specific.** No internal product, customer, environment, host name,
      IP address, ticket number or log from a Versaterm system. Reproduce issues with generic
      SIPp scenarios and documentation addresses (RFC 5737 / example.com).
- [ ] Commits are authored with a `@versaterm.com` identity. Contributions are Versaterm's
      and say so openly.

## Patches carried on `versaterm/main`

See [PATCHES.md](PATCHES.md).

## CI

| Workflow | What it runs | Role |
| --- | --- | --- |
| `CI` (`ci.yml`, upstream, unmodified) | Upstream build and `npm run test-ci` on `ubuntu-latest` | Shows what upstream CI will report on a PR. |
| `Versaterm CI` (`versaterm-ci.yml`) | The same steps, pinned to `ubuntu-24.04` | **Required check** (`upstream-tests`). |

The pinned copy exists because a moving `ubuntu-latest` image can break upstream CI for
reasons unrelated to the code. In November 2026, the move to newer Boost and CMake broke the
build of `v0.9.12` and of upstream `main`. Keep its steps identical to upstream `ci.yml`. The
sync workflow warns when upstream changes `ci.yml`.

## Images

The `Versaterm image` workflow builds `docker/versaterm/Dockerfile` from the sources of the
branch or tag. This Dockerfile is the upstream Dockerfile, changed to build the checked-out
sources instead of cloning upstream. The workflow publishes:

| Trigger | Tags on `ghcr.io/versaterm-public-safety/drachtio-server` |
| --- | --- |
| push to `versaterm/main` | `versaterm-main`, `sha-<short>` |
| tag `vX.Y.Z-vt.N` | `vX.Y.Z-vt.N`, `sha-<short>` |

Consumers should pin by digest, as printed in the workflow run summary.

The upstream `Docker` workflow (`docker-publish.yml`) is disabled on this fork. It pushes to
Docker Hub with upstream's credentials and build infrastructure.

## Keeping up with upstream

The `Versaterm upstream sync` workflow runs weekly, and on demand. It:

- fast-forwards `main` to `upstream/main`;
- reports a newer upstream release than the base of `versaterm/main`;
- lists `versaterm/main` commits that upstream already contains (`git cherry`), which can be
  dropped;
- fails if `versaterm/main` no longer rebases cleanly onto `upstream/main`.

If upstream changes files under `.github/workflows`, updating `main` needs a `SYNC_TOKEN`
secret with `contents` and `workflows` write permission.

### Rebasing `versaterm/main` on a new upstream release (maintainer)

```sh
git fetch upstream --tags
git switch versaterm/main && git pull
git switch -c versaterm/rebase-vX.Y.Z
git rebase --onto vX.Y.Z <old-base-tag>   # commits already upstream are skipped
# resolve conflicts, drop merged patches, update PATCHES.md
git push origin versaterm/rebase-vX.Y.Z   # CI + image build must pass
git push --force-with-lease origin versaterm/rebase-vX.Y.Z:versaterm/main
git tag vX.Y.Z-vt.1 versaterm/main && git push origin vX.Y.Z-vt.1
```

## Repository settings

- `main`: force pushes and deletion blocked.
- `versaterm/main`: pull request, code-owner review, linear history and green
  `upstream-tests` and `image` checks required. Administrators may bypass to do a release
  rebase.
- Code owners: [.github/CODEOWNERS](.github/CODEOWNERS).
