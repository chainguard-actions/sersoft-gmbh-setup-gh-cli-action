<!-- markdownlint-disable -->

# Hardening Report: sersoft-gmbh--setup-gh-cli-action/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sersoft-gmbh--setup-gh-cli-action/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and the composite action reference external actions using mutable tags or branch names instead of pinned 40-character commit SHAs. This exposes the pipeline to supply-chain attacks if the referenced tag or branch is updated maliciously.

Failing references:
- tests.yml: `actions/checkout@v5` (3 occurrences), `sersoft-gmbh/setup-gh-cli-action@main` (3 occurrences)
- deploy.yml: `actions/checkout@v5`
- codeql.yml: `sersoft-gmbh/oss-common-actions/.github/workflows/codeql-ts-gh-action.yml@main`
- tag-update.yml: `actions/checkout@v5`, `sersoft-gmbh/running-release-tags-action@v4`
- .github/actions/generate-action-code/action.yml: `actions/setup-node@v6`

Locations:

- `.github/workflows/tests.yml:24`
- `.github/workflows/tests.yml:27`
- `.github/workflows/deploy.yml:13`
- `.github/workflows/codeql.yml:18`
- `.github/workflows/tag-update.yml:12`
- `.github/workflows/tag-update.yml:13`
- `.github/actions/generate-action-code/action.yml:6`

### github-env-injection (severity: high)

In tests.yml, three `install-gh` steps (one per job: test-specific-version, test-rolling-version, test-no-token) write values sourced from `steps.*.outputs.*` (BRANCH_INSTALLED_VERSION from `steps.install-gh-main.outputs.installed-version` and LOCAL_INSTALLED_VERSION from `steps.install-gh-local.outputs.installed-version`) to `$GITHUB_OUTPUT` via env vars without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). Step outputs are untrusted sources per the check rules, and a newline-containing value could inject additional key=value pairs into GITHUB_OUTPUT.

Example failing pattern (repeated in all three jobs):
```
env:
  BRANCH_INSTALLED_VERSION: ${{ steps.install-gh-main.outputs.installed-version }}
  LOCAL_INSTALLED_VERSION: ${{ steps.install-gh-local.outputs.installed-version }}
run: |
  echo "installed-version=${BRANCH_INSTALLED_VERSION}" >> "${GITHUB_OUTPUT}"  # FAIL: no sanitization
  echo "installed-version=${LOCAL_INSTALLED_VERSION}" >> "${GITHUB_OUTPUT}"   # FAIL: no sanitization
```

Locations:

- `.github/workflows/tests.yml:43`
- `.github/workflows/tests.yml:80`
- `.github/workflows/tests.yml:114`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all unpinned action references by resolving real commit SHAs via lookup_action_sha: actions/checkout@v5→fbc6f39, sersoft-gmbh/setup-gh-cli-action@main→9b0daf4, sersoft-gmbh/oss-common-actions@main→f545fa6, sersoft-gmbh/running-release-tags-action@v4→662ab3f, actions/setup-node@v6→249970. Fixed github-env-injection in all three jobs in tests.yml by adding `safe=$(printf '%s' "${VAR}" | tr -d '\n\r')` sanitization before writing step outputs to GITHUB_OUTPUT.

