<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action--docker/v2.26.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action--docker/v2.26.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: an expansion of workflow-controlled data is not quoted. In the 'Publish Test Results' step, `platform="$DOCKER_PLATFORM"` (where DOCKER_PLATFORM comes from inputs.docker_platform) is later used as `docker run ${platform:+--platform $platform} \`. The inner `$platform` has no quotes, so the shell splits it on whitespace and expands glob characters. A caller can then inject extra `docker run` arguments, for example `linux/amd64 -v /:/host --privileged`. Fix: `${platform:+--platform "$platform"}`.

Locations:

- `action.yml:231`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In action.yml, changed `${platform:+--platform $platform}` to `${platform:+--platform "$platform"}` so docker_platform input is passed as a single argument and cannot inject extra docker run flags; still drops out entirely when empty.

