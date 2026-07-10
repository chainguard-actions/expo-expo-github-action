<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/9.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **expo--expo-github-action/9.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference external actions using mutable version tags (@v4, @v7) instead of pinned 40-character SHA commits. This exposes the workflow to supply-chain attacks if the upstream action tag is moved. Failing references include: actions/checkout@v4, actions/github-script@v7, actions/upload-artifact@v4.

Locations:

- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:36`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:72`
- `.github/workflows/review.yml:16`
- `.github/workflows/review.yml:36`
- `.github/workflows/test.yml:36`
- `.github/workflows/test.yml:55`
- `.github/workflows/test.yml:100`
- `.github/workflows/test.yml:120`
- `.github/workflows/test.yml:152`
- `.github/workflows/test.yml:172`
- `.github/workflows/test.yml:200`
- `.github/workflows/test.yml:230`
- `.github/workflows/test.yml:250`

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. In release.yml, the 'Update tags' step uses: `run: git tag --force v${{ steps.version.outputs.result }} && git push --force --tags`. The value of steps.version.outputs.result is injected directly into the shell command string without quoting or env-var indirection, enabling shell metacharacter injection if the step output contains special characters.

Locations:

- `.github/workflows/release.yml:80`

### hardcoded-credentials (severity: high)

A literal non-expression token value 'badtoken' is assigned to the 'github-token' input in test.yml. The pattern `github-token: badtoken` matches the hardcoded credentials check (token name with a literal alphanumeric value that is not a ${{ secrets.* }} expression).

Locations:

- `.github/workflows/test.yml:252`

### missing-permissions (severity: medium)

None of the three workflow files (release.yml, review.yml, test.yml) define a top-level `permissions:` block, and no individual job within any of these files defines a `permissions:` block either. Without explicit permissions, workflows run with the default (potentially broad) GITHUB_TOKEN permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/review.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all four findings across release.yml, review.yml, and test.yml:
1. unpinned-uses: Pinned all external actions to full 40-char SHAs with tag comments: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/github-script@v7 → @f28e40c7f34bde8b3046d885e986cb6290c5673b, actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02.
2. script-injection: In release.yml 'Update tags' step, moved ${{ steps.version.outputs.result }} into an env var VERSION and used "v${VERSION}" in the shell command.
3. hardcoded-credentials: In test.yml, replaced literal 'badtoken' with ${{ secrets.GITHUB_TOKEN }}.
4. missing-permissions: Added top-level permissions blocks to all three files: release.yml gets 'contents: write' (needed for git push/tags), review.yml and test.yml get 'contents: read'.

### Iteration 2

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings: (1) script-injection in .github/workflows/test.yml line 91 — moved `${{ secrets.EXPO_PROJECT_ID }}` out of the `run:` shell string into an `env:` block as `EXPO_PROJECT_ID`, referenced as `"$EXPO_PROJECT_ID"` in the shell command; (2) unpinned-uses in .github/actions/setup/action.yml — pinned `oven-sh/setup-bun@v2` to SHA `0c5077e51419868618aeaa5fe8019c62421857d6` and `actions/setup-node@v4` to SHA `49933ea5288caeca8642d1e84afbd3f7d6820020`, with original tags preserved as comments.

