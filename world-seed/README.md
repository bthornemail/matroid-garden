# World Seed Mirror (v1)

Build static deploy output:

```bash
./scripts/build-world-seed.sh
```

Output path:

```text
dist/world-seed/v1/
```

Deploy to nginx root (example):

```bash
sudo rsync -av --delete dist/world-seed/v1/ /var/www/matroid-garden/v1/
```

Then browse:
- `/v1/index.html`
- `/v1/replay/index.html`
- `/v1/bundle/core.bundle.json`
