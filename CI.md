# CI Gates (v1)

This file defines mandatory checks for merge into `main` while v1 is active.

## Required Checks
From repo root:

```bash
# Browser-first runtime
node runtime/browser-v1/tests/smoke.mjs
node runtime/browser-v1/tests/signature-hooks.mjs
node runtime/browser-v1/tests/merkle.mjs
node runtime/browser-v1/tests/merkle-ordering.mjs
node runtime/browser-v1/tests/logical-clock.mjs

# POSIX/Haskell runtime
cd runtime/posix-haskell
cabal build exe:ulp-runtime
./test/cli-smoke.sh
./test/vector-check.sh
./test/reverse-vector-check.sh

# Signature + replay interop suites
export ULP_TEST_PRIVKEY=0x0123456789012345678901234567890123456789012345678901234567890123
./test/sig-suite.sh
./test/replay-suite.sh
./test/interop-suite.sh
```

## Artifact Expectations
CI uploads at minimum:
- vector fixtures from `runtime/posix-haskell/test/vectors/`
- generated fingerprints from replay suite (if present in temp outputs)

## Merge Policy
- Any failure in required checks blocks merge.
- Any change to frozen contract surface (wire/hash/merkle/order/replay semantics) requires v2 process from `CONTRACT.md`.

## Branch Protection Setup (GitHub)
Configure branch protection on `main` with:
- Require a pull request before merging.
- Require status checks to pass before merging.
- Required check: `interop-v1 / interop` (workflow `.github/workflows/interop.yml`, job `interop`).
- Require branches to be up to date before merging.
- Disable force pushes.
- Disable branch deletion.
- Do not allow direct pushes to `main` except trusted admins (recommended: disable admin bypass too).

CLI setup example (`gh` + `jq`):

```bash
OWNER=FreedomAutonomyReciprocitySoverignty
REPO=fano-garden
BRANCH=main

gh api \
  --method PUT \
  -H "Accept: application/vnd.github+json" \
  "/repos/$OWNER/$REPO/branches/$BRANCH/protection" \
  --input - <<'JSON'
{
  "required_status_checks": {
    "strict": true,
    "checks": [
      { "context": "interop-v1 / interop" }
    ]
  },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 1,
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": false,
    "require_last_push_approval": false
  },
  "restrictions": null,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "block_creations": false,
  "required_conversation_resolution": true,
  "lock_branch": false,
  "allow_fork_syncing": true
}
JSON
```

## Label Routing (Swarm)
Suggested labels for parallel work allocation:
- `area:runtime-js`
- `area:runtime-hs`
- `area:interop`
- `area:projection-svg`
- `area:projection-glb`
- `area:docs`

A lightweight router workflow may auto-assign by label, but does not bypass required checks.
