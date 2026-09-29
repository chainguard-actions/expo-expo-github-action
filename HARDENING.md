<!-- markdownlint-disable -->

# Hardening Report: expo--expo-github-action/9.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **expo--expo-github-action/9.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action at .github/actions/setup/action.yml references two external actions using mutable tag-based refs instead of full 40-character SHA commit digests. This exposes the action to supply-chain attacks: if the upstream tag is moved or the repository is compromised, malicious code could be injected silently.

Failing references:
- `uses: oven-sh/setup-bun@v2` (line 17) — should be pinned to a full SHA, e.g. `oven-sh/setup-bun@<40-hex-sha> # v2`
- `uses: actions/setup-node@v4` (line 21) — should be pinned to a full SHA, e.g. `actions/setup-node@<40-hex-sha> # v4`

Locations:

- `.github/actions/setup/action.yml:17`
- `.github/actions/setup/action.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both unpinned action references in hardened/action/.github/actions/setup/action.yml:
- `oven-sh/setup-bun@v2` → `oven-sh/setup-bun@0c5077e51419868618aeaa5fe8019c62421857d6 # v2` (line 17)
- `actions/setup-node@v4` → `actions/setup-node@49933ea5288caeca8642d1e84afbd3f7d6820020 # v4` (line 21)
SHAs were resolved via git ls-remote against the upstream repositories.

