<!-- markdownlint-disable -->

# Hardening Report: sersoft-gmbh--setup-gh-cli-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sersoft-gmbh--setup-gh-cli-action/v2.0.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action step uses `actions/setup-node@v4`, which is a mutable tag reference rather than a pinned full 40-character commit SHA. This means the action could silently change if the tag is moved, enabling supply-chain attacks. It should be pinned to a specific commit SHA (e.g., `actions/setup-node@1d0ff469b12f2e5c5b9e3b5d7b5e5e5e5e5e5e5e # v4`).

Locations:

- `.github/actions/generate-action-code/action.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `actions/setup-node@v4` to its full commit SHA `49933ea5288caeca8642d1e84afbd3f7d6820020` in `.github/actions/generate-action-code/action.yml`, preserving the `# v4` comment for readability. This prevents supply-chain attacks from mutable tag references.

