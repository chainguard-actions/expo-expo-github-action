<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action at .github/actions/setup/action.yml references two external actions using mutable tag refs instead of pinned 40-character SHA digests. This exposes the action to supply-chain attacks if the upstream tags are moved or overwritten.

Failing references:
- Line 18: `uses: oven-sh/setup-bun@v1` (tag `v1`, not a SHA)
- Line 23: `uses: actions/setup-node@v3` (tag `v3`, not a SHA)

These should be pinned to full commit SHAs, e.g.:
  uses: oven-sh/setup-bun@4bc047ad259df6fc24a6c9b0f9a0cb08cf17fbe # v1
  uses: actions/setup-node@1a4442cacd436585916779262731d1f68db9430 # v3

Locations:

- `.github/actions/setup/action.yml:18`
- `.github/actions/setup/action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned two unpinned action references in hardened/action/.github/actions/setup/action.yml:
- oven-sh/setup-bun@v1 → oven-sh/setup-bun@f4d14e03ff726c06358e5557344e1da148b56cf7 # v1
- actions/setup-node@v3 → actions/setup-node@3235b876344d2a9aa001b8d1453c930bba69e610 # v3

Both SHAs were resolved using lookup_action_sha and the original tag is preserved as a comment for readability.

