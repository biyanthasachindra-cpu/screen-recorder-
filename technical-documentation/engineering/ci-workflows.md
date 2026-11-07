# Continuous integration

FrameCast Studio uses distributor-neutral source checks. The upstream OpenScreen release, registry, announcement, sponsorship, and package-manager workflows are intentionally not part of this derivative.

## GitHub Actions

The only workflow is [`ci.yml`](../../.github/workflows/ci.yml). It runs on pushes and pull requests to `main`, plus manual dispatch, with `contents: read` permission.

The Ubuntu verification job runs:

- `npm run i18n:check`
- `npm run lint`
- `npm run docs:check`
- application TypeScript checking
- test TypeScript checking with a larger Node heap
- `npm run build-vite`

A separate matrix runs `npm test` on Ubuntu, Windows, and macOS. Ubuntu installs FFmpeg and sets `FRAMECAST_FFMPEG_TEST_PATH=/usr/bin/ffmpeg`, so Voice Polish creates and decodes real media rather than only testing mocked process arguments.

No job publishes an installer or uses a release token. The normal read-only GitHub token is sufficient.

## GitLab CI

[`.gitlab-ci.yml`](../../.gitlab-ci.yml) provides Linux `verify`, `unit-and-integration-tests`, and `real-ffmpeg-test` jobs. Its cache key is `package-lock.json`.

GitLab's default pipeline does not claim Windows/macOS native certification. A project with its own runners can add native build jobs after installing the required SDKs and signing credentials.

## What CI proves

A green run shows that source checks, unit/integration tests, and the web/Electron bundle build pass on the reported runners. The real FFmpeg job also proves the Voice Polish output can be decoded and its source remains byte-identical.

The matrix is useful for platform-conditional JavaScript and filesystem behavior. It is not a substitute for testing a packaged app with OS permissions, a real display, webcam, microphone, GPU, and desktop portal.

## What remains manual

- Native window/display recording and permission prompts
- Real microphone, system-audio, webcam, and cursor-device combinations
- Installed helper/compositor dynamic-library loading
- Preview and exported-frame inspection on actual GPUs
- Signing, notarization, installer update, and rollback behavior

Use the platform scripts in `package.json` and the inherited manual checklist as a starting point. Historical checklist entries are evidence about upstream OpenScreen builds, not about a FrameCast package.

## Adding a job

Keep credentials out of the tree. Use the smallest possible token permissions and never add upstream service credentials. Document any new artifact, secret, network destination, or release side effect here and in [Git and CI](../../docs/GIT_AND_CI.md).

Do not add automatic publishing to a pull-request job. Native packages should be built and inspected on their target OS before a separate, deliberate release step uploads them.

## Updating this page

Change it in the same commit as any CI behavior change. Broken relative links are checked by `npm run docs:check`; runner results and secrets are not inferred by that checker.
