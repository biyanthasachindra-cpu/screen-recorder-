# Release and secrets policy

FrameCast Studio `0.1.0` has no automated publisher, signed installer, official store listing, or distributor update feed. This page defines the safe boundary for adding one.

## Current release process

Build on each target OS using the platform command in [Development and packaging](../../docs/DEVELOPMENT.md). Test the installed artifact, including capture and export, before uploading it manually to the new project's own Releases page.

Label unsigned artifacts as development builds. Do not describe them as notarized, Store-approved, auto-updating, or official OpenScreen packages.

There is no release branch freeze, milestone bot, registry publisher, Discord announcement, or stable/prerelease promotion workflow in this repository.

## Forbidden inherited credentials and identities

Do not reuse or restore:

- OpenScreen GitHub owner/repository release configuration
- upstream PostHog tokens or analytics endpoints with an upstream token
- upstream Microsoft Store product IDs, publisher IDs, or signing identities
- another maintainer's Apple ID, team ID, notary profile, or certificate
- upstream Homebrew, winget, AUR, Flatpak, Snap, Nix, or sponsorship accounts
- upstream Discord bots, webhooks, or community automation

Compatibility names in source code are not permission to publish under an upstream identity.

## Secrets a distributor may eventually need

- macOS signing certificate and notarization credentials
- Windows signing certificate and, if applicable, Partner Center credentials
- package-manager credentials for repositories the distributor actually owns
- a distributor-owned update repository and signing/rollback process
- an optional distributor-owned analytics project with a published payload and opt-out

Store secrets in the CI provider's protected secret store, never in `.env.example`, source, tests, issues, or workflow logs. Restrict them to protected branches/tags and use the least privilege available.

## Update boundary

`FRAMECAST_GITHUB_REPOSITORY` is empty by default, which disables release checks. Setting it to `owner/repository` selects that GitHub repository for the inherited checker. It does not by itself prove that packaged update metadata or installation is safe.

Before exposing updates, inspect the generated metadata, confirm signing, test installation over a previous FrameCast build, test failure recovery, and verify that no artifact or URL points to OpenScreen.

## Analytics boundary

`FRAMECAST_POSTHOG_TOKEN` is also empty by default. No usage event can be planned without a token. A distributor adding one must document the exact payload, retention, legal basis/consent requirements, and opt-out. `DO_NOT_TRACK`, CI, development mode, and the local setting remain vetoes.

## Versioning

Update `package.json` and `package-lock.json` together with `npm version <version> --no-git-tag-version`, then review the diff. Use semantic versions and annotated tags only after the corresponding commit passes CI and native QA.

## Provenance and attribution

Keep the upstream repository/commit in `package.json`, retain all MIT notices, and regenerate/review third-party notices when dependencies change. A release must include the license information required by its bundled native/media dependencies.

## Before any public release

1. Confirm the Git remote and every packaged URL belong to the new project.
2. Run the full source verification chain and real FFmpeg test.
3. Test native capture, microphone, webcam, transcription, editing, and export on the target machine.
4. Inspect the packaged native helpers and compositor for missing or stale libraries.
5. Test a clean install and record the exact OS/architecture results.
6. Scan the source and artifact for credentials and unintended service endpoints.
7. Publish deliberately; never as a side effect of a pull-request check.
