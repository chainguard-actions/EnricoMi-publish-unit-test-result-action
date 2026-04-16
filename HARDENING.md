# Hardening Report: EnricoMi-publish-unit-test-result-action

> This file was generated automatically by the hardening agent.

**Policy SHA:** `81c40e4a38868610038a3fe9622e72150fdcf324`

Action **EnricoMi-publish-unit-test-result-action** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses `runs.using: docker` with a mutable image tag instead of an immutable SHA digest. The image reference `docker://ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0` uses the tag `v2.23.0`, which can be silently overwritten to point to a different (potentially malicious) image. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/enricomi/publish-unit-test-result-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:9243`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker container image reference in action.yml from the mutable tag `ghcr.io/enricomi/publish-unit-test-result-action:v2.23.0` to the immutable digest `ghcr.io/enricomi/publish-unit-test-result-action@sha256:9c5a81dbdc3bdc370fdb9c4e35a8ceb63cce8c880354d3db0d6e66f4197db55a # v2.23.0`. The digest was resolved via the Docker Registry HTTP API v2.

