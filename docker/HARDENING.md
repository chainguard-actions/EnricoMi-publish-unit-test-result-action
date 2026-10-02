<!-- markdownlint-disable -->

# Hardening Report: EnricoMi--publish-unit-test-result-action--docker/v2.24.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **EnricoMi--publish-unit-test-result-action--docker/v2.24.0** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in action.yml line 178. Changed `${platform:+--platform $platform}` to `${platform:+--platform "$platform"}` so that the caller-controlled `docker_platform` input value is always treated as a single quoted argument to docker, preventing shell metacharacter injection.

