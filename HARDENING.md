<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action/v2.24.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action/v2.24.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell command strings. Although github.action_path is not attacker-controlled, any ${{ ... }} expression directly inside a run: block is a script-injection finding. Affected lines: (1) `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt` and (2) `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_QUERY"`.

Locations:

- `misc/action/find-workflows/action.yml:26`
- `misc/action/find-workflows/action.yml:35`

### script-injection (severity: high)

Rule (a): ${{ github.action_path }} is interpolated directly inside run: shell command strings. Affected lines: (1) `pip install --force --no-cache-dir -r ${{ github.action_path }}/requirements.txt` and (2) `python ${{ github.action_path }}/script.py "$SCRIPT_URL" "$SCRIPT_REPO" "$SCRIPT_PACKAGE"`.

Locations:

- `misc/action/package-downloads/action.yml:33`
- `misc/action/package-downloads/action.yml:43`

### script-injection (severity: high)

Rule (b): Unquoted shell variable expansions of input-derived env vars in the docker run step. (1) `"$DOCKER_REGISTRY/$DOCKER_IMAGE:$DOCKER_TAG"` — DOCKER_REGISTRY, DOCKER_IMAGE, and DOCKER_TAG are set from inputs.docker_registry, inputs.docker_image, and inputs.docker_tag respectively, and are used unquoted (no surrounding double-quotes) as the final docker image argument, allowing shell metacharacter injection. (2) `${platform:+--platform $platform}` — the inner `$platform` (sourced from inputs.docker_platform via DOCKER_PLATFORM) is unquoted inside the expansion, allowing word-splitting and glob expansion.

Locations:

- `docker/action.yml:196`
- `docker/action.yml:138`

### unpinned-uses (severity: high)

The root action.yml uses a Docker image referenced by a mutable version tag rather than an immutable SHA digest: `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.24.0'`. A tag can be overwritten to point to a different image, enabling supply-chain attacks. It should be pinned to a SHA digest, e.g. `image: 'docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:156`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed 4 findings across 4 files: (1) Pinned Docker image in action.yml to immutable SHA digest sha256:4b0f12ca591495ab7c177753bc4142f1b679f7f95df9595f19c31a986021001d while preserving docker:// scheme and tag. (2) Fixed script injection in misc/action/find-workflows/action.yml by moving ${{ github.action_path }} into env: blocks as ACTION_PATH for both the pip install and python run steps. (3) Fixed script injection in misc/action/package-downloads/action.yml with the same pattern. (4) Fixed script injection in docker/action.yml by replacing the unsafe ${platform:+--platform $platform} expansion (unquoted inner $platform) with a bash array platform_args=() that properly quotes the value when non-empty.

