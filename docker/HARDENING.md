<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action--docker/v2.25.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action--docker/v2.25.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: unquoted shell variable expansion of untrusted data. In the `run:` block, `platform` is assigned from `$DOCKER_PLATFORM` (which is set from `${{ inputs.docker_platform }}`), and then used unquoted inside a conditional expansion: `docker run ${platform:+--platform $platform}`. The inner `$platform` is not double-quoted, so if the input contains shell metacharacters (spaces, semicolons, pipes, etc.), the shell will perform word splitting and glob expansion on it, enabling command injection. The fix is to use `${platform:+--platform "$platform"}`.

Locations:

- `action.yml:239`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in action.yml at the docker run command. Changed `${platform:+--platform $platform}` to `${platform:+--platform "$platform"}` to properly quote the platform variable derived from `inputs.docker_platform`, preventing word splitting and glob expansion on untrusted input.

