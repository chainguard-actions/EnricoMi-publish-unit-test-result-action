<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.23.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action/v2.23.0** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ inputs.docker_platform }} is interpolated directly inside the run: shell script on the line `platform="${{ inputs.docker_platform }}"`. Additionally, ${{ inputs.docker_registry }}, ${{ inputs.docker_image }}, and ${{ inputs.docker_tag }} are interpolated directly in the docker run command at the end of the run: block. An attacker controlling these inputs can inject arbitrary shell commands.

Locations:

- `docker/action.yml:200`
- `docker/action.yml:260`

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell scripts in two steps: the pip install step (`pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`) and the Find workflows step (`python ${{ github.action_path }}/script.py ${{ inputs.url }} ${{ inputs.query }}`). The inputs.url and inputs.query values are also interpolated directly, allowing an attacker to inject arbitrary shell commands.

Locations:

- `misc/action/find-workflows/action.yml:27`
- `misc/action/find-workflows/action.yml:37`

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell scripts in two steps: the pip install step (`pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt`) and the Get download info step (`python ${{ github.action_path }}/script.py ${{ inputs.url }} ${{ inputs.repo }} ${{ inputs.package }}`). The inputs.url, inputs.repo, and inputs.package values are also interpolated directly, allowing an attacker to inject arbitrary shell commands.

Locations:

- `misc/action/package-downloads/action.yml:33`
- `misc/action/package-downloads/action.yml:43`

### script-injection (severity: high)

Rule (a): ${{ inputs.json }} is interpolated directly inside a heredoc in the run: shell script (`cat <<EOF\n      ${{ inputs.json }}\n      EOF`). An attacker controlling the json input can inject arbitrary content that escapes the heredoc and executes shell commands.

Locations:

- `misc/action/json-output/action.yml:60`

### unpinned-uses (severity: high)

The runs.image field references a mutable Docker image tag instead of an immutable SHA digest: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0'`. The tag v2.23.0 can be overwritten at any time, enabling a supply-chain attack. It should be replaced with a SHA digest reference such as `docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:135`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 5 findings:
1. action.yml: Pinned 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0' to immutable digest sha256:9c5a81dbdc3bdc370fdb9c4e35a8ceb63cce8c880354d3db0d6e66f4197db55a while preserving the docker:// scheme and tag.
2. docker/action.yml: Moved inputs.docker_platform, inputs.docker_registry, inputs.docker_image, and inputs.docker_tag into the env: block as INPUT_DOCKER_PLATFORM, INPUT_DOCKER_REGISTRY, INPUT_DOCKER_IMAGE, INPUT_DOCKER_TAG; replaced direct ${{ }} interpolations in the run: block with env var references.
3. misc/action/find-workflows/action.yml: Moved github.action_path into ACTION_PATH env var for both steps; moved inputs.url and inputs.query into INPUT_URL and INPUT_QUERY env vars; updated run: blocks to use env vars.
4. misc/action/package-downloads/action.yml: Moved github.action_path into ACTION_PATH env var for both steps; moved inputs.url, inputs.repo, inputs.package into INPUT_URL, INPUT_REPO, INPUT_PACKAGE env vars; updated run: blocks to use env vars.
5. misc/action/json-output/action.yml: Added INPUT_JSON env var to the 'JSON output' step's env: block; replaced the heredoc that directly interpolated ${{ inputs.json }} with 'printf '%s\n' "$INPUT_JSON"' to avoid heredoc injection.

### Iteration 2

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed 6 findings across 4 workflow files:
1. ci-cd.yml: Moved `${{ steps.action.outputs.image }}` into env block as ACTION_IMAGE to prevent script injection in docker manifest inspect command.
2. test-results.yml: Pinned `EnricoMi/publish-unit-test-result-action/composite@master` to full SHA `c9bbcabcaf28c5ce2a9f8b6ee91c62873071b2ce`. Also moved `${{ fromJSON( steps.test-results.outputs.json ).conclusion }}` into env block as CONCLUSION to prevent script injection in case statement.
3. badges.yml: Moved `${{ steps.downloads.outputs.total_downloads }}` and `${{ steps.downloads.outputs.recent_downloads_per_day }}` into env block and used printf with %s format specifiers. Also moved `${{ steps.workflows.outputs.total_workflows }}` into env block with printf.
4. test-os.yml: Added top-level `permissions: {}` block to restrict default permissions.

