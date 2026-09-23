<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.2.0** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both unpinned action references in hardened/action/.github/actions/setup/action.yml:
- `oven-sh/setup-bun@v1` → `oven-sh/setup-bun@f4d14e03ff726c06358e5557344e1da148b56cf7 # v1`
- `actions/setup-node@v3` → `actions/setup-node@3235b876344d2a9aa001b8d1453c930bba69e610 # v3`
Both SHAs were resolved via git ls-remote against the upstream repositories.

