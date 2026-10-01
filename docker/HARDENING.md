<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action--docker/v2.23.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action--docker/v2.23.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The `run:` block in action.yml directly interpolates `${{ inputs.docker_platform }}` into a shell command string: `platform="${{ inputs.docker_platform }}"`. This allows an attacker-controlled input to be embedded verbatim into the shell command before the shell ever sees it, enabling command injection. Additionally, `${{ inputs.docker_registry }}`, `${{ inputs.docker_image }}`, and `${{ inputs.docker_tag }}` are interpolated directly into the final `docker run` argument: `${{ inputs.docker_registry }}/${{ inputs.docker_image }}:${{ inputs.docker_tag }}`. All four of these `${{ inputs.* }}` expressions must be moved to `env:` variables and then referenced as quoted shell variables (e.g., `"$INPUT_DOCKER_REGISTRY"`) instead of being interpolated directly in the `run:` script.

Locations:

- `action.yml:184`
- `action.yml:270`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker_platform }}" appears directly in run: block of step "Publish Test Results"; move to env: map

Locations:

- `action.yml:225`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker_registry }}" appears directly in run: block of step "Publish Test Results"; move to env: map

Locations:

- `action.yml:317`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker_image }}" appears directly in run: block of step "Publish Test Results"; move to env: map

Locations:

- `action.yml:317`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker_tag }}" appears directly in run: block of step "Publish Test Results"; move to env: map

Locations:

- `action.yml:317`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all five findings (which all relate to the same root cause — ${{ inputs.docker_* }} expressions interpolated directly into the run: block). Added INPUT_DOCKER_PLATFORM, INPUT_DOCKER_REGISTRY, INPUT_DOCKER_IMAGE, and INPUT_DOCKER_TAG to the step's env: block. In the run: block, replaced `platform="${{ inputs.docker_platform }}"` with `platform="$INPUT_DOCKER_PLATFORM"`, and replaced the final docker image argument `${{ inputs.docker_registry }}/${{ inputs.docker_image }}:${{ inputs.docker_tag }}` with `"$INPUT_DOCKER_REGISTRY/$INPUT_DOCKER_IMAGE:$INPUT_DOCKER_TAG"`. All ${{ }} expressions for docker inputs are now safely in the env: block only.

