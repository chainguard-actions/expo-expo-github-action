<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference third-party actions using mutable version tags instead of pinned full-length SHA digests, making them vulnerable to supply-chain attacks if the tag is moved.

release.yml: actions/checkout@v3, actions/setup-node@v3 (used in 3 jobs), actions/github-script@v6
review.yml: actions/checkout@v3, actions/setup-node@v3, actions/upload-artifact@v3
test.yml: actions/checkout@v3, actions/setup-node@v3, actions/github-script@v6

Locations:

- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:63`
- `.github/workflows/review.yml:21`
- `.github/workflows/review.yml:24`
- `.github/workflows/review.yml:48`
- `.github/workflows/test.yml:37`
- `.github/workflows/test.yml:62`
- `.github/workflows/test.yml:63`
- `.github/workflows/test.yml:91`
- `.github/workflows/test.yml:108`
- `.github/workflows/test.yml:131`
- `.github/workflows/test.yml:143`
- `.github/workflows/test.yml:157`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level `permissions:` key, and no individual job within them defines a `permissions:` block. Without explicit permissions, workflows inherit the default repository permissions (which may include write access to contents, pull requests, etc.), violating the principle of least privilege.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/review.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Direct GitHub Actions expression interpolation inside `run:` shell command strings (rule a).

1. release.yml — The 'Update tags' step interpolates `${{ steps.version.outputs.result }}` directly into a shell command: `run: git tag --force v${{ steps.version.outputs.result }} && git push --force --tags`. The `steps.*` context is a workflow-controllable value that flows through YAML template substitution before the shell sees it, enabling injection of arbitrary shell metacharacters.

2. test.yml — The 'Configure project' step interpolates `${{ secrets.EXPO_PROJECT_ID }}` directly into a shell command: `eas init --id ${{ secrets.EXPO_PROJECT_ID }} --force --non-interactive`. Any expression inside a run: block is a script-injection risk regardless of the source context.

Locations:

- `.github/workflows/release.yml:74`
- `.github/workflows/test.yml:73`

### hardcoded-credentials (severity: high)

A literal hardcoded token value is assigned to the `github-token` input in test.yml: `github-token: badtoken`. The field name contains 'token' and the value is a non-expression alphanumeric literal. Hardcoded credential values should never appear in workflow files; use `${{ secrets.* }}` expressions instead.

Locations:

- `.github/workflows/test.yml:155`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection, hardcoded-credentials

**Notes:**

Fixed all four findings across release.yml, review.yml, and test.yml:

1. unpinned-uses: Pinned all action references to full commit SHAs — actions/checkout@v3→a37ce91, actions/setup-node@v3→3235b87, actions/github-script@v6→d7906e4, actions/upload-artifact@v3→ff15f03.

2. missing-permissions: Added top-level `permissions:` blocks — release.yml gets `contents: write` (needed for git tag push and semantic-release), review.yml and test.yml get `contents: read`.

3. script-injection: (a) release.yml 'Update tags' step: moved `${{ steps.version.outputs.result }}` into env var `VERSION_RESULT` and referenced as `"v${VERSION_RESULT}"` in shell. (b) test.yml 'Configure project' step: moved `${{ secrets.EXPO_PROJECT_ID }}` into env var `EXPO_PROJECT_ID` and referenced as `"$EXPO_PROJECT_ID"` in shell.

4. hardcoded-credentials: Replaced literal `badtoken` in test.yml's `github-token:` field with `${{ secrets.GITHUB_TOKEN }}`.

