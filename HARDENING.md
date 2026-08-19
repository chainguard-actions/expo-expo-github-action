<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow and composite action files reference external actions using mutable tag refs instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the tag is moved.

Failing references:
- .github/workflows/release.yml: actions/checkout@v4, actions/github-script@v7
- .github/workflows/review.yml: actions/checkout@v4, actions/upload-artifact@v3
- .github/workflows/test.yml: actions/checkout@v4, actions/github-script@v7
- .github/actions/setup/action.yml: oven-sh/setup-bun@v1, actions/setup-node@v3

Locations:

- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:36`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:68`
- `.github/workflows/review.yml:16`
- `.github/workflows/review.yml:38`
- `.github/workflows/test.yml:36`
- `.github/workflows/test.yml:57`
- `.github/actions/setup/action.yml:16`
- `.github/actions/setup/action.yml:20`

### script-injection (severity: high)

GitHub Actions expressions (${{ ... }}) are interpolated directly inside run: shell command strings, allowing the substituted value to be interpreted as shell code before the shell ever sees it.

(a) release.yml — steps.version.outputs.result is interpolated directly into a git tag command:
  run: git tag --force v${{ steps.version.outputs.result }} && git push --force --tags

(a) test.yml — secrets.EXPO_PROJECT_ID is interpolated directly into an eas init command:
  run: eas init --id ${{ secrets.EXPO_PROJECT_ID }} --force --non-interactive

Both should be moved to env: variables and referenced as quoted shell variables (e.g. "$VERSION").

Locations:

- `.github/workflows/release.yml:80`
- `.github/workflows/test.yml:87`

### hardcoded-credentials (severity: high)

A literal non-expression value is assigned to a field named 'github-token' in .github/workflows/test.yml:

  github-token: badtoken

This is a hardcoded token value (not a ${{ secrets.* }} expression). Even if used for testing purposes, hardcoded credential-like values in workflow files are a security risk and should be replaced with secret references or removed.

Locations:

- `.github/workflows/test.yml:302`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level permissions: key, and no individual job within them defines a job-level permissions: key. Without explicit permissions, workflows inherit the default repository permissions (which may be overly broad, e.g. write access to contents and pull requests). Each workflow should declare the minimal permissions required.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/review.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all four findings across four files:

1. unpinned-uses: Pinned all 5 external action references to full 40-char SHAs with tag comments preserved: actions/checkout@v4, actions/github-script@v7, actions/upload-artifact@v3, oven-sh/setup-bun@v1, actions/setup-node@v3.

2. script-injection: (a) release.yml git tag step: moved `steps.version.outputs.result` into env var VERSION, used `"v$VERSION"` in run. (b) test.yml eas init step: moved `secrets.EXPO_PROJECT_ID` into env var EXPO_PROJECT_ID, used `"$EXPO_PROJECT_ID"` in run.

3. hardcoded-credentials: Replaced literal `badtoken` value in test.yml's `github-token:` field with `${{ secrets.GITHUB_TOKEN }}`.

4. missing-permissions: Added top-level `permissions: contents: write` to release.yml (needs to push tags), `permissions: contents: read` to review.yml and test.yml.

