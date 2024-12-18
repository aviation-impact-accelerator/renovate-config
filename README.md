# Default renovatebot configuration

Default config for the organization which is extended by the default renovatebot onboarding config

## The default config:

The [config:recommended](https://docs.renovatebot.com/presets-config/#configrecommended) preset is extended as a base.

This configuration includes:

- The [dependency dashboard issue](https://docs.renovatebot.com/presets-default/#dependencydashboard) as a preset.
- Update lockfiles when dependencies are updated.
- Group all minor and patch updates in each Poetry dependency group (dev and docs).
- Group terraform dependencies.
- Group GitHub Actions (GHA) dependencies.
- Enables pre-commit dependency updates (currently in beta testing) and groups pre-commit dependencies.
- Enables git-submodule pointer updates (currently in beta testing) and groups submodule updates.

## Other presets

This repository also contains other presets which you may want to additionally extend from in
order to better align with how each repository uses renovatebot.

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

Group rules are applied in sequence and if a package matches a later group, it is removed from the earlier group. In order to ensure that ruff updates are always grouped together, extend the config with `groupRuff` at the bottom of the `extends` list.
