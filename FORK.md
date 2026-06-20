# FORK.md

This is a fork of [`chopratejas/headroom`](https://github.com/chopratejas/headroom). This file
tracks what diverges from upstream so the fork stays easy to rebase and reason about.

> Keep this file updated whenever you add, change, or drop a fork-specific patch, or sync with upstream.

## Remotes

| Remote     | URL                                       | Role                        |
| ---------- | ----------------------------------------- | --------------------------- |
| `origin`   | `git@github.com:edipizarro/headroom.git`  | This fork (push here)       |
| `upstream` | `git@github.com:chopratejas/headroom.git` | Source project (pull-only)  |

- **Working branch:** `edipizarro`
- **`main`** tracks upstream cleanly — keep it free of fork-only commits so it can fast-forward.

## Divergence from upstream

Fork-specific commits live on `edipizarro`. As of this writing:

### `scripts/headroom-standalone` — pipe-friendly compressor
- Commit: `d52ac1ee feat(scripts): add headroom-standalone pipe-friendly compressor`
- Reads arbitrary text from stdin, runs the full compression pipeline
  (`ContentRouter` → `SmartCrusher` / `CodeCompressor` / `Kompress`), writes compressed
  text to stdout and savings stats to stderr.
- Frames input as a generic `tool_result` (not `Read`/`Bash`/`Grep`, which are in
  `DEFAULT_EXCLUDE_TOOLS` and would be protected) so compression always applies.
- Allowlisted in `.gitignore`'s `scripts/` block.
- Usage: `cat file.js | headroom-standalone [--ratio 0.3 | --stats-only | --json]`

## Syncing with upstream

```bash
git fetch upstream
git checkout main
git merge --ff-only upstream/main      # main should always fast-forward
git push origin main

# Re-apply fork patches on top of fresh upstream
git checkout edipizarro
git rebase main
# resolve conflicts, then:
git push --force-with-lease origin edipizarro
```

## Checking divergence

```bash
git fetch upstream
git log --oneline upstream/main..edipizarro   # fork-only commits (should match this file)
git log --oneline edipizarro..upstream/main   # upstream commits not yet pulled in
```
