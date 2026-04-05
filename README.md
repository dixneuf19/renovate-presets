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

### Available presets

| Preset | Description |
|--------|-------------|
| `local>dixneuf19/renovate-presets` | Default: `config:recommended`, automerge minor, branch automerge, weekly lock file maintenance, 3-day release cooldown |
| `local>dixneuf19/renovate-presets:python` | Disable automerge for Python base image updates |

### Example with Python preset

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>dixneuf19/renovate-presets",
    "local>dixneuf19/renovate-presets:python"
  ]
}
```
