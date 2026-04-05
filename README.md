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

- `config:recommended` (dependency dashboard, semantic commits, monorepo grouping, etc.)
- Automerge minor/patch updates (merged directly to branch, no PR noise)
- Weekly lock file maintenance
- 3-day release cooldown (`minimumReleaseAge`) for supply chain protection
- Python Docker base image updates require manual review (no automerge)
