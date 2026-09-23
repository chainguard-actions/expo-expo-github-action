<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.2.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action at `.github/actions/setup/action.yml` references two external actions using mutable version tags instead of full 40-character commit SHAs. This exposes the action to supply-chain attacks where a tag could be silently moved to point to malicious code:
- `uses: oven-sh/setup-bun@v1` (tag `v1` is mutable)
- `uses: actions/setup-node@v3` (tag `v3` is mutable)
These should be pinned to their full SHA digests, e.g. `uses: oven-sh/setup-bun@<40-char-sha> # v1`.

Locations:

- `.github/actions/setup/action.yml:18`
- `.github/actions/setup/action.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both mutable action references in hardened/action/.github/actions/setup/action.yml to full 40-character commit SHAs:
- `oven-sh/setup-bun@v1` → `oven-sh/setup-bun@f4d14e03ff726c06358e5557344e1da148b56cf7 # v1`
- `actions/setup-node@v3` → `actions/setup-node@3235b876344d2a9aa001b8d1453c930bba69e610 # v3`
The original tag names are preserved as inline comments for readability.

