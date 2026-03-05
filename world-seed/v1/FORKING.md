# Fork From Here

## Fork Pattern (No Central API)
1. Copy `/v1/canon/canon-manifest.ndjson` and referenced `canon-*.ndjson` files.
2. Append your own `canon-<name>.ndjson` file(s).
3. Add your series entry in your manifest.
4. Replay from your manifest URL in `/v1/replay/index.html`.
5. Export media from the replay page and publish your bundle under your own `/v1`.

## Rules to Preserve Interop
- Keep NDJSON one-object-per-line.
- Preserve ordered array semantics where used.
- Do not include mnemonic/private key material in artifacts.
- Use deterministic canonicalization/hashing rules from the v1 contract docs.

## Suggested Layout
```
/v1/
  /canon/
    canon-manifest.ndjson
    canon-*.ndjson
  /bundle/
    core.bundle.json
  /replay/
    index.html
```
