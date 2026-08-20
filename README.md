# renovate-presets

Shareable [Renovate](https://docs.renovatebot.com/) config presets for my repos.

## Usage

Reference in your repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>dixneuf19/renovate-presets"]
}
```

### What's included

- `config:best-practices` (dependency dashboard, semantic commits, monorepo grouping, Docker/GitHub Actions digest pinning, abandoned package warnings, config migration, weekly lock file maintenance)
- Automerge minor/patch updates (merged directly to branch, no PR noise)
- Automerge digest updates, so pinned Docker/GitHub Actions digests refresh on their own
- 3-day release cooldown (`minimumReleaseAge`) for supply chain protection
- OSV vulnerability alerts (includes OpenSSF malicious packages feed)
- Python Docker base image updates require manual review (no automerge)
