<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.25.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action/v2.25.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell command strings in two steps. This causes the expression to be substituted by the YAML template engine before the shell ever sees it, which is a script-injection risk. Offending lines: (1) `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt` and (2) `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_QUERY"`. Fix: use the $GITHUB_ACTION_PATH environment variable instead.

Locations:

- `misc/action/find-workflows/action.yml:29`
- `misc/action/find-workflows/action.yml:38`

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell command strings in two steps. Offending lines: (1) `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt` and (2) `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_REPO" "$SCRIPT_PACKAGE"`. Fix: use the $GITHUB_ACTION_PATH environment variable instead.

Locations:

- `misc/action/package-downloads/action.yml:31`
- `misc/action/package-downloads/action.yml:41`

### script-injection (severity: high)

Rule (b): The env var DOCKER_PLATFORM (sourced from inputs.docker_platform) is expanded unquoted inside the run: script as `${platform:+--platform $platform}`. The inner `$platform` is not double-quoted, so shell metacharacters in the input value can break out of the argument and inject arbitrary shell commands. Fix: use `${platform:+--platform "$platform"}` to ensure the value is always quoted when present.

Locations:

- `docker/action.yml:246`

### unpinned-uses (severity: high)

The runs.image field in action.yml references a Docker image by mutable tag (v2.25.0) rather than an immutable SHA digest: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.25.0'`. A tag can be silently overwritten to point to a different (potentially malicious) image. Fix: pin to a SHA digest, e.g. `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:243`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed four security findings: (1) In misc/action/find-workflows/action.yml, replaced both occurrences of `${{ github.action_path }}` with `$GITHUB_ACTION_PATH` to eliminate template-engine script injection risk. (2) In misc/action/package-downloads/action.yml, replaced both occurrences of `${{ github.action_path }}` with `$GITHUB_ACTION_PATH` for the same reason. (3) In docker/action.yml, added double-quotes around `$platform` inside the conditional expansion (`${platform:+--platform "$platform"}`) to prevent shell metacharacter injection from the docker_platform input. (4) In action.yml, pinned the Docker image `ghcr.io/enricomi/publish-unit-test-result-action:v2.25.0` to its immutable SHA digest `sha256:c94dd210d38e0b4cbed1e3812faf17893823a3dd6385759252af3bbe152a810d`, preserving the `docker://` scheme and the tag inline.

