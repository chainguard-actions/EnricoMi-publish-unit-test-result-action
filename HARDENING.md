<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.24.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **EnricoMi--publish-unit-test-result-action/v2.24.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses a Docker image reference with a mutable version tag instead of a SHA digest: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.24.0'`. This is vulnerable to supply-chain attacks because the tag can be silently updated to point to a different image.

Locations:

- `action.yml:9`

### script-injection (severity: high)

Sub-rule (a): Direct `${{ }}` expression interpolation inside `run:` shell command strings. `${{ github.action_path }}` is substituted by the YAML template engine before the shell sees it, allowing shell metacharacter injection. Offending lines:
- Line 26: `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`
- Line 34: `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_QUERY"`

Locations:

- `misc/action/find-workflows/action.yml:26`
- `misc/action/find-workflows/action.yml:34`

### script-injection (severity: high)

Sub-rule (a): Direct `${{ }}` expression interpolation inside `run:` shell command strings. `${{ github.action_path }}` is substituted by the YAML template engine before the shell sees it, allowing shell metacharacter injection. Offending lines:
- Line 33: `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`
- Line 41: `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_REPO" "$SCRIPT_PACKAGE"`

Locations:

- `misc/action/package-downloads/action.yml:33`
- `misc/action/package-downloads/action.yml:41`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Three fixes applied: (1) Pinned Docker image in action.yml from mutable tag 'v2.24.0' to SHA256 digest 'sha256:4b0f12ca591495ab7c177753bc4142f1b679f7f95df9595f19c31a986021001d' with tag preserved as comment. (2) Fixed script injection in misc/action/find-workflows/action.yml by moving both ${{ github.action_path }} expressions to env: blocks as ACTION_PATH and referencing them as $ACTION_PATH in shell. (3) Fixed script injection in misc/action/package-downloads/action.yml by moving both ${{ github.action_path }} expressions to env: blocks as ACTION_PATH and referencing them as $ACTION_PATH in shell.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability in docker/action.yml at line 297. The `$platform` variable inside the shell conditional expansion `${platform:+--platform $platform}` was unquoted, allowing shell metacharacters in the `docker_platform` input to be interpreted by the shell. Changed to `${platform:+--platform "$platform"}` to properly double-quote the variable inside the expansion, preventing command injection.

