<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.23.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action/v2.23.0** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses a Docker image referenced by a mutable tag (`v2.23.0`) rather than an immutable SHA digest. This means the image could be silently replaced with a malicious version. The failing reference is: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0'`. It should be replaced with a SHA-digest reference such as `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:170`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.docker_registry }}`, `${{ inputs.docker_image }}`, and `${{ inputs.docker_tag }}` are interpolated directly inside the `run:` shell command string in the 'Publish Test Results' step. An attacker who controls these inputs can inject arbitrary shell commands. The offending line is: `${{ inputs.docker_registry }}/${{ inputs.docker_image }}:${{ inputs.docker_tag }}`. These values should be passed via `env:` variables and referenced as quoted shell variables instead.

Locations:

- `docker/action.yml:230`

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings. In the 'Install Python dependencies' step: `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`. In the 'Find workflows' step: `python ${{ github.action_path }}/script.py ${{ inputs.url }} ${{ inputs.query }}`. The `inputs.url` and `inputs.query` values are attacker-controlled and can inject arbitrary shell commands. All expressions should be moved to `env:` variables and referenced as quoted shell variables.

Locations:

- `misc/action/find-workflows/action.yml:27`
- `misc/action/find-workflows/action.yml:35`

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings. In the 'Install Python dependencies' step: `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`. In the 'Get download info' step: `python ${{ github.action_path }}/script.py ${{ inputs.url }} ${{ inputs.repo }} ${{ inputs.package }}`. The `inputs.url`, `inputs.repo`, and `inputs.package` values are attacker-controlled and can inject arbitrary shell commands. All expressions should be moved to `env:` variables and referenced as quoted shell variables.

Locations:

- `misc/action/package-downloads/action.yml:34`
- `misc/action/package-downloads/action.yml:42`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.json }}` is interpolated directly inside a `run:` shell heredoc in the 'JSON output' step: `cat <<EOF\n      ${{ inputs.json }}\n      EOF`. The `inputs.json` value is attacker-controlled and can break out of the heredoc or inject shell commands. This value should be passed via an `env:` variable and referenced as a quoted shell variable.

Locations:

- `misc/action/json-output/action.yml:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 5 findings:
1. action.yml: Pinned Docker image 'ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0' with SHA256 digest sha256:9c5a81dbdc3bdc370fdb9c4e35a8ceb63cce8c880354d3db0d6e66f4197db55a.
2. docker/action.yml: Moved ${{ inputs.docker_platform }}, ${{ inputs.docker_registry }}, ${{ inputs.docker_image }}, and ${{ inputs.docker_tag }} from inline run: shell string into the env: block as DOCKER_PLATFORM, DOCKER_REGISTRY, DOCKER_IMAGE, DOCKER_TAG; referenced as quoted shell variables.
3. misc/action/find-workflows/action.yml: Moved ${{ github.action_path }}, ${{ inputs.url }}, and ${{ inputs.query }} into env: blocks as ACTION_PATH, INPUT_URL, INPUT_QUERY; referenced as quoted shell variables.
4. misc/action/package-downloads/action.yml: Moved ${{ github.action_path }}, ${{ inputs.url }}, ${{ inputs.repo }}, and ${{ inputs.package }} into env: blocks as ACTION_PATH, INPUT_URL, INPUT_REPO, INPUT_PACKAGE; referenced as quoted shell variables.
5. misc/action/json-output/action.yml: Replaced the heredoc containing ${{ inputs.json }} with echo "$JSON" using the already-defined JSON env var from the same step's env: block.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability in hardened/action/docker/action.yml. Changed `${platform:+--platform $platform}` to `${platform:+--platform "$platform"}` in the `docker run` command of the 'Publish Test Results' step. This ensures the `$platform` variable (sourced from `inputs.docker_platform` via the `DOCKER_PLATFORM` env var) is properly double-quoted within the conditional expansion, preventing shell metacharacters or spaces in the input from being interpreted as shell commands.

