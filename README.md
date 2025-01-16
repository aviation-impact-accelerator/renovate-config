# Default renovatebot configuration

Default config for the organization which is extended by the default renovatebot onboarding config

## The default config:

The [config:recommended](https://docs.renovatebot.com/presets-config/#configrecommended) preset is extended as a base.

This configuration includes:

- The [dependency dashboard issue](https://docs.renovatebot.com/presets-default/#dependencydashboard) as a preset.
- Update lockfiles when dependencies are updated.
- Request review from the `aia-software` team on CI failure during the update.
- [Default group presets](#default-group-presets).
- Scheduled updates between 0300-0400 every Monday London time.
- Enable copier template updates.

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

Automatic git submodule updates can be grouped together. This feature is currently in beta testing, so it needs to be [enabled](https://docs.renovatebot.com/modules/manager/git-submodules/#enabling) to opt-in. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupGitSubmodules"
  ]
}
```

### Group Poetry updates by dependency group

Poetry dependencies in the aviation-impact-accelerator organization are grouped into `dev` and `docs` dependency groups. This will only group patch and minor versions together so major package versions (with breaking changes) can be handled with individually. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupPoetry"
  ]
}
```

### Group pre-commit hook updates

pre-commit hooks can be grouped together. This feature is currently in beta testing, so it needs to be [enabled](https://docs.renovatebot.com/modules/manager/pre-commit/#enabling) to opt-in. Pre-commit hooks typically have an automatic fix option so it is safe to group all version updates together - both major and minor. Extend your config with:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupPreCommit"
  ]
}
```

### Group Ruff updates

Ruff versions are defined in the `pyproject.toml` and `.pre-commit-config.yaml`. To keep them in sync, renovate can group the ruff updates to a single PR. You can opt-in to this by extending the config:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:groupRuff"
  ]
}
```

Group rules are applied in sequence and if a package matches a later group, it is removed from the earlier group. In order to ensure that ruff updates are always grouped together, extend the config with `groupRuff` at the bottom of the `extends` list. `groupRuff` overlaps with ruff updates in `groupPoetry` and `groupPreCommit` so if you wish for ruff updates to be grouped together, ensure that the `groupRuff` is defined belo `groupPoetry` and `groupPreCommit` to override those grouping rules.

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

Automerge is enabled by default for certain dependency groups in `default.json`. To disable this feature for a repository, add this to the config:

```json
{
  "extends": [
    // ... other presets ...
    "local>aviation-impact-accelerator/renovate-config:disableAutomerge.json"
  ]
}
```

Make sure this is added after extending the default config to ensure this overrides the default.
