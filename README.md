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
- Automerge minor/patch updates, through a PR so repo checks gate the merge
- Automerge digest updates, so pinned Docker/GitHub Actions digests refresh on their own
- 3-day release cooldown (`minimumReleaseAge`) for supply chain protection
- OSV vulnerability alerts (includes OpenSSF malicious packages feed)
- Python Docker base image updates require manual review (no automerge)
- `0.x` minor updates require manual review, since per semver they may break anything
- Release cooldown relaxed to `timestamp-optional` on registries that publish no release timestamp, so their updates are not held back forever
- No release cooldown on digest updates, which have no release date of their own

### Why automerge goes through a PR

`:automergeBranch` is deliberately not used. It commits straight to the base
branch and only opens a PR when tests fail, which is quieter but silently
disables the checks that matter. Repo CI is usually wired to `on: pull_request`,
so on a bare Renovate branch it never runs. Renovate then sees no status check,
declines to automerge without one, and falls back to opening a PR anyway. The
result is the worst of both: the PR noise is still there and nothing merges on
its own.

With the default `automergeType: "pr"` the PR is opened first, the repo's checks
run against it, and the merge happens once they are green.

`platformAutomerge` additionally needs "Allow auto-merge" enabled on the
repository. Without it Renovate still merges, just on its next run after the
checks pass rather than immediately.

### Why `0.x` minors are not automerged

Semver says a `0.y.z` release may break anything on a `y` bump. Renovate does
not encode that: `getUpdateType()` compares version segments positionally, so
`0.11.1 -> 0.13.2` is a `minor` and `:automergeMinor` would merge it without
review. In practice that can be a whole major application upgrade.

Renovate does have an `isBreaking` flag that gets this right, and it can be read
from `packageRules` via `matchJsonata: ["isBreaking = true"]`. That does not help
for `oci://` charts: `isBreaking` comes from the versioning module, the `docker`
versioning module does not implement it, and the fallback is
`updateType === 'major'`, which is false here. So the same immich bump that
`helm` versioning reports as breaking is reported as non-breaking once the chart
is consumed over OCI, and a JSONata rule would let it through.

This is worth knowing if you reach for `isBreaking` elsewhere: it is reliable
under `semver`, `npm`, `helm`, `cargo`, `composer` and `python` versioning, and
absent under `docker` and everything else built on `GenericVersioningApi`.

The rule is written as `matchCurrentVersion: "/^0\\./"` rather than `"<1.0.0"`
on purpose. A range goes through `versioningApi.matches()`, which `docker`
versioning inherits from `GenericVersioningApi` as plain equality, so `"<1.0.0"`
silently matches nothing for exactly the deps that need it most. The regex form
is evaluated before the range path and works under every versioning module.

### Release cooldown and container registries

Renovate's `docker` datasource only reports a release timestamp for Docker Hub,
where it reads `tag_last_pushed`. Every other registry returns nothing usable.
Since `minimumReleaseAgeBehaviour` defaults to `timestamp-required`, an update
with no timestamp is marked pending forever: no branch, no PR, just a permanent
entry under "Pending Status Checks" on the dependency dashboard.

This also catches `oci://` Helm chart dependencies, which Renovate resolves
through the `docker` datasource rather than the `helm` one.

The preset therefore sets `minimumReleaseAgeBehaviour: timestamp-optional` for
the registries we use that cannot be aged. Docker Hub keeps the full 3-day
cooldown, since there it actually works.

Upstream: [renovate#37196](https://github.com/renovatebot/renovate/issues/37196),
[renovate#38656](https://github.com/renovatebot/renovate/issues/38656),
[renovate#39064](https://github.com/renovatebot/renovate/issues/39064).
