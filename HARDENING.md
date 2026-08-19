<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.2.1** was hardened automatically. 4 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of immutable full SHA digests, making them vulnerable to supply-chain attacks if the tag is moved. Failing references include: actions/checkout@v4, actions/github-script@v7, and actions/upload-artifact@v3.

Locations:

- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:39`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:72`
- `.github/workflows/review.yml:19`
- `.github/workflows/review.yml:41`
- `.github/workflows/test.yml:35`
- `.github/workflows/test.yml:63`
- `.github/workflows/test.yml:93`
- `.github/workflows/test.yml:110`
- `.github/workflows/test.yml:133`
- `.github/workflows/test.yml:152`
- `.github/workflows/test.yml:175`
- `.github/workflows/test.yml:188`
- `.github/workflows/test.yml:209`

### script-injection (severity: high)

Sub-rule (a) violation: In the 'Update tags' step of release.yml, the expression `${{ steps.version.outputs.result }}` is interpolated directly inside a `run:` shell command: `run: git tag --force v${{ steps.version.outputs.result }} && git push --force --tags`. The `steps.*.outputs.*` context flows through YAML template substitution before the shell processes it, allowing injection of shell metacharacters. The value should be assigned to an env var and that env var double-quoted in the shell command.

Locations:

- `.github/workflows/release.yml:88`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level `permissions:` key, and no individual jobs define their own `permissions:` blocks. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/review.yml:1`
- `.github/workflows/test.yml:1`

### hardcoded-credentials (severity: high)

In test.yml, the last step of the `preview-comment` job sets `github-token: badtoken` — a literal alphanumeric string value assigned to a token input field. This is a hardcoded credential (matches pattern: token: + alphanumeric value). It should be replaced with a secrets expression such as `${{ secrets.GITHUB_TOKEN }}`.

Locations:

- `.github/workflows/test.yml:218`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions, hardcoded-credentials

**Notes:**

Fixed all four findings across release.yml, review.yml, and test.yml:

1. unpinned-uses: Pinned all three actions to full commit SHAs — actions/checkout@v4 → 11d5960a326750d5838078e36cf38b85af677262, actions/github-script@v7 → f28e40c7f34bde8b3046d885e986cb6290c5673b, actions/upload-artifact@v3 → ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5. All occurrences across all three workflow files updated.

2. script-injection: In release.yml's 'Update tags' step, moved `${{ steps.version.outputs.result }}` into an env: block as VERSION and referenced it as "${VERSION}" in the shell command.

3. missing-permissions: Added top-level permissions blocks — release.yml gets `contents: write` (needed for git push/tagging), review.yml and test.yml get `contents: read`.

4. hardcoded-credentials: Replaced `github-token: badtoken` in test.yml's preview-comment job with `github-token: ${{ secrets.GITHUB_TOKEN }}`.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at line 97. Moved `${{ secrets.EXPO_PROJECT_ID }}` from direct interpolation in the `run:` shell command into an `env:` block as `EXPO_PROJECT_ID`, and updated the shell command to reference it as the quoted variable `"$EXPO_PROJECT_ID"`. This prevents any special characters in the secret value from being interpreted by the shell.

### Iteration 3

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both external action references in hardened/action/.github/actions/setup/action.yml to full commit SHAs: `oven-sh/setup-bun@v1` → `oven-sh/setup-bun@f4d14e03ff726c06358e5557344e1da148b56cf7 # v1` and `actions/setup-node@v3` → `actions/setup-node@3235b876344d2a9aa001b8d1453c930bba69e610 # v3`. The original version tags are preserved as inline comments for readability.

