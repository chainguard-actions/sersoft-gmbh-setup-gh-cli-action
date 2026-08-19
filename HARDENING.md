<!-- markdownlint-disable -->

# Hardening Report: sersoft-gmbh--setup-gh-cli-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sersoft-gmbh--setup-gh-cli-action/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and the composite action reference actions by mutable tags or branch names instead of immutable full SHA commits, making them vulnerable to supply-chain attacks. Failing references: .github/workflows/codeql-analysis.yml uses actions/checkout@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3; .github/workflows/deploy.yml uses actions/checkout@v4; .github/workflows/tag-update.yml uses actions/checkout@v4 and sersoft-gmbh/running-release-tags-action@v3; .github/workflows/tests.yml uses actions/checkout@v4 and sersoft-gmbh/setup-gh-cli-action@main (branch ref); .github/actions/generate-action-code/action.yml uses actions/setup-node@v4.

Locations:

- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:37`
- `.github/workflows/deploy.yml:11`
- `.github/workflows/tag-update.yml:10`
- `.github/workflows/tag-update.yml:11`
- `.github/workflows/tests.yml:18`
- `.github/workflows/tests.yml:23`
- `.github/workflows/tests.yml:55`
- `.github/workflows/tests.yml:60`
- `.github/workflows/tests.yml:91`
- `.github/workflows/tests.yml:96`
- `.github/actions/generate-action-code/action.yml:6`

### missing-permissions (severity: medium)

Three workflow files have no top-level permissions key and no job-level permissions key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially broad) permissions. Affected files: .github/workflows/deploy.yml, .github/workflows/tag-update.yml, .github/workflows/tests.yml.

Locations:

- `.github/workflows/deploy.yml:1`
- `.github/workflows/tag-update.yml:1`
- `.github/workflows/tests.yml:1`

### github-env-injection (severity: high)

In tests.yml, three run blocks (one per test job: test-specific-version, test-rolling-version, test-no-token) write values sourced from steps.*.outputs.* (via env vars BRANCH_INSTALLED_VERSION set from ${{ steps.install-gh-main.outputs.installed-version }} and LOCAL_INSTALLED_VERSION set from ${{ steps.install-gh-local.outputs.installed-version }}) directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'). Step outputs are untrusted inputs that can contain newline characters enabling injection of arbitrary key-value pairs into the GitHub output environment. Failing lines: echo "installed-version=${BRANCH_INSTALLED_VERSION}" >> "${GITHUB_OUTPUT}" and echo "installed-version=${LOCAL_INSTALLED_VERSION}" >> "${GITHUB_OUTPUT}".

Locations:

- `.github/workflows/tests.yml:43`
- `.github/workflows/tests.yml:45`
- `.github/workflows/tests.yml:80`
- `.github/workflows/tests.yml:82`
- `.github/workflows/tests.yml:116`
- `.github/workflows/tests.yml:118`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all findings across 5 files:

1. unpinned-uses: Pinned all 7 action references to full commit SHAs with tag comments preserved:
   - actions/checkout@v4 → SHA 11d5960a...
   - github/codeql-action/{init,autobuild,analyze}@v3 → SHA b7351df7...
   - sersoft-gmbh/running-release-tags-action@v3 → SHA 890dc5c1...
   - sersoft-gmbh/setup-gh-cli-action@main → SHA 9b0daf4f...
   - actions/setup-node@v4 → SHA 49933ea5...

2. missing-permissions: Added top-level `permissions: {}` to deploy.yml, tag-update.yml, and tests.yml. Added minimal job-level permissions (contents: write for deploy/tag-update jobs that push commits/tags; contents: read for test jobs).

3. github-env-injection: In all three test jobs (test-specific-version, test-rolling-version, test-no-token) in tests.yml, sanitized step output values before writing to GITHUB_OUTPUT using `safe=$(printf '%s' "${VAR}" | tr -d '\n\r')` pattern.

