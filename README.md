# Default renovatebot configuration

Default config for the organization which is extended by the default renovatebot onboarding config

## The default config:

The [config:recommended](https://docs.renovatebot.com/presets-config/#configrecommended) preset is extended as a base.

This configuration includes:

- The [dependency dashboard issue](https://docs.renovatebot.com/presets-default/#dependencydashboard) as a preset.
- Update lockfiles when dependencies are updated.
- [Automerge](#automerge-and-review) patch and minor updates once CI checks pass and the release is at least 3 days old.
- Request review from the `aia-software` team. For automerged updates, review is only requested if CI fails; other updates request review when the PR is opened.
- [Default group presets](#default-group-presets).
- Scheduled updates between 0300-0400 every Monday London time.
- Enable copier template updates.
- [prek support](#prek-support) for `prek.toml` files.
- [Custom managers for Copier templates](#copier-template-managers).

### Automerge and review

Updates automerge once CI checks pass, except for these, which need review from the `aia-software` team:

- Major updates, including grouped PRs that contain a major update.
- Minor updates of `0.x` packages (e.g. Ruff `0.16` to `0.17`), because `0.x` packages can make breaking changes in a minor release. Patch updates of `0.x` packages still automerge.
- New Python minor versions (e.g. `3.14` to `3.15` in `.python-version`). Python patch versions still automerge.
- Copier template updates.

Renovate only suggests an update once the release is at least 3 days old (`minimumReleaseAge`), so a yanked or compromised release is less likely to be merged. It also gives projects that publish to more than one place time to finish (e.g. Ruff's PyPI release and `ruff-pre-commit` tag), so grouped updates arrive in one PR. Pending updates are listed on the dependency dashboard. Security fixes skip the wait. Updates from sources without release timestamps (git tags and refs, Docker registries other than Docker Hub, and the internal `camia-*` packages) are not held back.

### prek support

Renovate has no prek manager, and its built-in `pre-commit` manager only reads `.pre-commit-config.yaml`. The default config adds a regex manager that updates the `rev` of each GitHub-hosted repo in `prek.toml` (and `prek.toml.jinja` Copier templates). Entries with `repo = "local"` are skipped. These updates are grouped with the other pre-commit hook updates by [`groupPreCommit`](#group-pre-commit-hook-updates).

### Copier template managers

Copier templates use `.jinja` files that Renovate's built-in managers don't read. The default config adds regex managers to update:

- Python dependency specifiers in `pyproject.toml.jinja`.
- pre-commit hook revisions in `prek.toml.jinja`.
- GitHub Actions and container images in workflow and action templates (`*.yml.jinja`, `action.yml.jinja`, and conditionally named workflow files).

Template GitHub Actions and pre-commit hooks are grouped with their non-template equivalents by [`groupGitHubActions`](#group-github-actions-updates) and [`groupPreCommit`](#group-pre-commit-hook-updates).

### Default group presets

Grouped updates reduce the noise renovate generates in repositories so it can be helpful to reduce the burden on maintainers. Some presets are included in the default configuration and if you are not using the default, you can extend your config with your own collection of presets.

### Group GitHub Actions updates

Packages and runner versions used in GitHub Actions workflows can be updated together. Major version updates still constitute an individual PR. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupGitHubActions"
  ]
}
```

### Group Git Submodule updates

Automatic git submodule updates can be grouped together. The git-submodules manager is disabled by default in Renovate and this preset [enables](https://docs.renovatebot.com/modules/manager/git-submodules/#enabling) it. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupGitSubmodules"
  ]
}
```

### Group uv updates

uv dependencies in the `dev` and `docs` [dependency groups](https://docs.astral.sh/uv/concepts/projects/dependencies/#dependency-groups) are grouped into one PR per group. Dependencies in the legacy `[tool.uv.dev-dependencies]` table are included in the `dev` group. This will only group patch and minor versions together so major package versions (with breaking changes) can be handled individually. Other dependency groups are not grouped.

uv itself (for example the `uv-pre-commit` hook or a `uv` pin) is grouped into a single `uv dependencies` PR. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupUv"
  ]
}
```

### Group pre-commit hook updates

pre-commit hooks can be grouped together. The pre-commit manager is disabled by default in Renovate and this preset [enables](https://docs.renovatebot.com/modules/manager/pre-commit/#enabling) it. Hooks found in `prek.toml` files and Copier templates are included in the same group. Pre-commit hooks typically have an automatic fix option so it is safe to group all version updates together - both major and minor. With the default config, a group containing a major update needs to be merged manually. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupPreCommit"
  ]
}
```

### Group Ruff updates

Ruff versions are defined in the `pyproject.toml` and in `.pre-commit-config.yaml` or `prek.toml`. To keep them in sync, renovate can group the ruff updates to a single PR. The preset also sets `rangeStrategy` to `bump`, so a lower bound such as `ruff>=0.15.12` is raised to the new version alongside the hook `rev`. Without it, Renovate leaves a range that the new version already satisfies untouched, so only the hook `rev` and `uv.lock` are updated. You can opt-in to this by extending the config:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupRuff"
  ]
}
```

Group rules are applied in sequence and if a package matches a later group, it is removed from the earlier group. `groupRuff` overlaps with ruff updates in `groupUv` and `groupPreCommit`, so to keep ruff updates grouped together, extend the config with `groupRuff` below `groupUv` and `groupPreCommit` in the `extends` list. For the same reason, `groupUv` should come below `groupPreCommit` so the `uv-pre-commit` hook is grouped with uv.

### Group Terraform dependencies

Terraform package versions can be updated together by extending the config:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupTerraform"
  ]
}
```

## Other presets

This repository also contains other presets which you may want to additionally extend from in
order to better align with how each repository uses renovatebot.

### Disable automerge

Automerge is enabled by default in `default.json` (see [Automerge and review](#automerge-and-review)). To disable this feature for a repository, add this to the config:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:disableAutomerge"
  ]
}
```

Make sure this is added after extending the default config to ensure this overrides the default.
