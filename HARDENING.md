<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/8.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/8.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action at .github/actions/setup/action.yml references two external actions using mutable tag-based refs instead of full 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the referenced tags are moved or overwritten. Failing references: `oven-sh/setup-bun@v1` and `actions/setup-node@v3`. These should be pinned to their full commit SHAs (e.g., `oven-sh/setup-bun@a3b90b8789999d8827c8eb599c266c4cc7c3f4df # v1`).

Locations:

- `.github/actions/setup/action.yml:16`
- `.github/actions/setup/action.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both mutable tag-based action references in hardened/action/.github/actions/setup/action.yml to full commit SHAs: `oven-sh/setup-bun@v1` → `oven-sh/setup-bun@f4d14e03ff726c06358e5557344e1da148b56cf7 # v1` and `actions/setup-node@v3` → `actions/setup-node@3235b876344d2a9aa001b8d1453c930bba69e610 # v3`.

