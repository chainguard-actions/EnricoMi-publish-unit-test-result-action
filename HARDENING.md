<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.23.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **EnricoMi--publish-unit-test-result-action/v2.23.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by mutable tag rather than a SHA digest: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0'`. A tag can be silently moved to point to a different (potentially malicious) image. The reference should use a SHA256 digest, e.g. `docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:9`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In misc/action/find-workflows/action.yml, the 'Install Python dependencies' step uses `${{ github.action_path }}` directly in a pip install command, and the 'Find workflows' step uses `${{ inputs.url }}` and `${{ inputs.query }}` directly as unquoted shell arguments to a Python script. Any of these values could contain shell metacharacters that would be interpreted by the shell before execution.

Locations:

- `misc/action/find-workflows/action.yml:30`
- `misc/action/find-workflows/action.yml:38`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In misc/action/package-downloads/action.yml, the 'Install Python dependencies' step uses `${{ github.action_path }}` directly in a pip install command, and the 'Get download info' step uses `${{ inputs.url }}`, `${{ inputs.repo }}`, and `${{ inputs.package }}` directly as unquoted shell arguments to a Python script. Attacker-controlled input values could inject shell commands.

Locations:

- `misc/action/package-downloads/action.yml:30`
- `misc/action/package-downloads/action.yml:38`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: heredoc block. In misc/action/json-output/action.yml, the 'JSON file' step uses `${{ inputs.json }}` directly in a run: heredoc (`cat <<EOF ... ${{ inputs.json }} ... EOF`). An attacker-controlled JSON input containing shell metacharacters or heredoc delimiters could break out of the heredoc and inject arbitrary shell commands.

Locations:

- `misc/action/json-output/action.yml:55`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: shell command. In docker/action.yml, the 'Publish Test Results' step uses `${{ inputs.docker_registry }}/${{ inputs.docker_image }}:${{ inputs.docker_tag }}` directly as an unquoted argument to the docker run command. Attacker-controlled values for these inputs could inject shell metacharacters or alter the docker command in unintended ways.

Locations:

- `docker/action.yml:270`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed 5 findings across 5 files:

1. **action.yml** (unpinned-uses): Pinned `docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0` to `docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:9c5a81dbdc3bdc370fdb9c4e35a8ceb63cce8c880354d3db0d6e66f4197db55a # v2.23.0`.

2. **misc/action/find-workflows/action.yml** (script-injection): Moved `${{ github.action_path }}` into `ACTION_PATH` env var in the 'Install Python dependencies' step; moved `${{ github.action_path }}`, `${{ inputs.url }}`, and `${{ inputs.query }}` into `ACTION_PATH`, `INPUT_URL`, `INPUT_QUERY` env vars in the 'Find workflows' step. Shell commands now reference `$ACTION_PATH`, `$INPUT_URL`, `$INPUT_QUERY`.

3. **misc/action/package-downloads/action.yml** (script-injection): Same pattern — moved `${{ github.action_path }}` to `ACTION_PATH` env var in 'Install Python dependencies'; moved `${{ github.action_path }}`, `${{ inputs.url }}`, `${{ inputs.repo }}`, `${{ inputs.package }}` to env vars `ACTION_PATH`, `INPUT_URL`, `INPUT_REPO`, `INPUT_PACKAGE` in 'Get download info' step.

4. **misc/action/json-output/action.yml** (script-injection): Replaced the heredoc `cat <<EOF ... ${{ inputs.json }} ... EOF` with `echo "$JSON"`, using the `JSON` env var already defined in the step. Also added `JSON: ${{ inputs.json }}` to the second step's env block since it was missing.

5. **docker/action.yml** (script-injection): Moved `${{ inputs.docker_platform }}`, `${{ inputs.docker_registry }}`, `${{ inputs.docker_image }}`, and `${{ inputs.docker_tag }}` into `INPUT_DOCKER_PLATFORM`, `INPUT_DOCKER_REGISTRY`, `INPUT_DOCKER_IMAGE`, `INPUT_DOCKER_TAG` env vars. The `run:` block now uses `$INPUT_DOCKER_PLATFORM`, `$INPUT_DOCKER_REGISTRY`, `$INPUT_DOCKER_IMAGE`, `$INPUT_DOCKER_TAG` shell variables.

