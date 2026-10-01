<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.24.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action/v2.24.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): `${{ github.action_path }}` is interpolated directly inside `run:` shell command strings in two steps. This causes the GitHub Actions expression to be substituted into the shell script before execution, which is a script injection risk. Offending lines:
- `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`
- `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_QUERY"`
These should use the `$GITHUB_ACTION_PATH` environment variable instead.

Locations:

- `misc/action/find-workflows/action.yml:25`
- `misc/action/find-workflows/action.yml:33`

### script-injection (severity: high)

Sub-rule (a): `${{ github.action_path }}` is interpolated directly inside `run:` shell command strings in two steps. This causes the GitHub Actions expression to be substituted into the shell script before execution, which is a script injection risk. Offending lines:
- `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`
- `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_REPO" "$SCRIPT_PACKAGE"`
These should use the `$GITHUB_ACTION_PATH` environment variable instead.

Locations:

- `misc/action/package-downloads/action.yml:33`
- `misc/action/package-downloads/action.yml:41`

### unpinned-uses (severity: high)

The root `action.yml` uses a Docker image referenced by a mutable tag (`v2.24.0`) rather than an immutable SHA digest. This means the image could be replaced with a different version without changing the action definition, creating a supply-chain risk. The `runs.image` field is: `docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.24.0`. It should be pinned to a SHA digest, e.g. `ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:163`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Three fixes applied: (1) Pinned the Docker image in action.yml from mutable tag `v2.24.0` to immutable digest `sha256:4b0f12ca591495ab7c177753bc4142f1b679f7f95df9595f19c31a986021001d`, preserving the `docker://` scheme and tag. (2) In misc/action/find-workflows/action.yml, replaced both `${{ github.action_path }}` expressions in `run:` steps with the `$GITHUB_ACTION_PATH` environment variable. (3) In misc/action/package-downloads/action.yml, replaced both `${{ github.action_path }}` expressions in `run:` steps with the `$GITHUB_ACTION_PATH` environment variable.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in hardened/action/docker/action.yml at the `docker run` command. Changed `${platform:+--platform $platform}` to `${platform:+--platform "$platform"}` to properly quote the inner expansion of `$platform`, preventing word-splitting and glob expansion on the attacker-controlled `inputs.docker_platform` value.

