<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/9.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/9.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable version-tag refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks. release.yml: actions/checkout@v4 (x3), actions/github-script@v7 (x1). review.yml: actions/checkout@v4, actions/upload-artifact@v4. test.yml: actions/checkout@v4 (x3), actions/github-script@v7 (x6).

Locations:

- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:64`
- `.github/workflows/release.yml:81`
- `.github/workflows/review.yml:18`
- `.github/workflows/review.yml:43`
- `.github/workflows/test.yml:39`
- `.github/workflows/test.yml:63`
- `.github/workflows/test.yml:213`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level permissions: block, and no individual job defines job-level permissions either. Without explicit permissions, workflows run with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/review.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

GitHub Actions expressions are interpolated directly inside run: shell command strings (sub-rule a). (1) release.yml line 99: `run: git tag --force v${{ steps.version.outputs.result }} && git push --force --tags` injects a steps.*.outputs.* value directly into the shell. (2) test.yml line 83: `eas init --id ${{ secrets.EXPO_PROJECT_ID }} --force --non-interactive` interpolates a secrets context expression directly in a run: block.

Locations:

- `.github/workflows/release.yml:99`
- `.github/workflows/test.yml:83`

### hardcoded-credentials (severity: high)

test.yml contains a hardcoded literal token value: `github-token: badtoken`. This is a non-expression literal (not a ${{ secrets.* }} reference) assigned to a field named github-token, matching the pattern token: [A-Za-z0-9]{8+}.

Locations:

- `.github/workflows/test.yml:248`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection, hardcoded-credentials

**Notes:**

Fixed all four findings across release.yml, review.yml, and test.yml:

1. unpinned-uses: Pinned all action references to full SHA commits with tag comments: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, actions/github-script@v7 → @f28e40c7f34bde8b3046d885e986cb6290c5673b, actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02.

2. missing-permissions: Added top-level permissions blocks — release.yml gets `contents: write` (needs to push tags), review.yml and test.yml get `contents: read`.

3. script-injection: (a) release.yml: moved `${{ steps.version.outputs.result }}` into env var VERSION_RESULT and used `"v${VERSION_RESULT}"` in the shell run. (b) test.yml: moved `${{ secrets.EXPO_PROJECT_ID }}` into env var EXPO_PROJECT_ID and used `"$EXPO_PROJECT_ID"` in the eas init command.

4. hardcoded-credentials: Replaced `github-token: badtoken` in test.yml with `github-token: ${{ secrets.GITHUB_TOKEN }}` to use a proper secret reference.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both external action references in hardened/action/.github/actions/setup/action.yml:
- `oven-sh/setup-bun@v2` → `oven-sh/setup-bun@0c5077e51419868618aeaa5fe8019c62421857d6 # v2`
- `actions/setup-node@v4` → `actions/setup-node@49933ea5288caeca8642d1e84afbd3f7d6820020 # v4`
Original version tags preserved as inline comments for readability.

