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
