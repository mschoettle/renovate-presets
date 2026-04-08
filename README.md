# Renovate Config Presets

This repository contains [reusable config presets](https://docs.renovatebot.com/config-presets/) that can be reused in your project's Renovate config.

## Usage

To use a preset in your project's config, add it to the [`extends`](https://docs.renovatebot.com/configuration-options/#extends) section of your project's Renovate config:

```json
{
    "extends": {
        [...]
        "github>mschoettle/renovate-presets//presets/<presetFileName>",
        [...]
    }
}
```

## Presets

All presets are located in the `presets` directory.

### [base](./presets/base.json5)

A base preset extending Renovate's [config:recommended](https://docs.renovatebot.com/presets-config/#configrecommended) preset.
It does not extend [config:best-practices](https://docs.renovatebot.com/presets-config/#configbest-practices) directly in order to set a blanket [minimum release age](https://docs.renovatebot.com/key-concepts/minimum-release-age/) of 7 days.
See the below table for commonalities and differences between this preset and `config:best-practices`:

| | `base` | `config:best-practices` |
| ------- | ------ | ----------------------- |
| Base Config | `config:recommended` | `config:recommended` |
| Enable Config Migration via [`:configMigration`](https://docs.renovatebot.com/presets-default/#configmigration) | Yes | Yes |
| Highlight abandoned packages via [`abondonments:recommended`](https://docs.renovatebot.com/presets-abandonments/) | Yes | Yes |
| Maintain Lock Files via [`:maintainLockFilesWeekly`](https://docs.renovatebot.com/presets-default/#maintainlockfilesweekly) | Yes | Yes |
| GitHub Actions digest pinning | Yes, via [`helpers:pinGitHubActionDigestsToSemver`](https://docs.renovatebot.com/presets-helpers/#helperspingithubactiondigeststosemver) (adds SemVer extraction and comments) | Yes, via [`helpers:pinGitHubActionDigests`](https://docs.renovatebot.com/presets-helpers/#helperspingithubactiondigests) |
| [Minimum Release Age](https://docs.renovatebot.com/key-concepts/minimum-release-age/) | 7 days across all package managers | 3 days for npm only via [`security:minimumReleaseAgeNpm`](https://docs.renovatebot.com/presets-security/#securityminimumreleaseagenpm) |
| Pin Docker digests via [`docker:pinDigests`](https://docs.renovatebot.com/presets-docker/#dockerpindigests) | No | Yes |
| Pin dependencies | Yes, via [`:pinAllExceptPeerDependencies`](https://docs.renovatebot.com/presets-default/#pinallexceptpeerdependencies) | Yes, via [`:pinDevDependencies`](https://docs.renovatebot.com/presets-default/#pindevdependencies) |

In addition, the `base` preset sets the following configuration options:

- Separate multiple major version updates via [`separateMultipleMajor`](https://docs.renovatebot.com/configuration-options/#separatemultiplemajor)
- Enable Alpine Docker image variant updates via [`docker-alpine.json5`](#docker-alpine) below
- Support version updates for version inputs in GitHub Actions via [`actions-dependency-version.json5`](#actions-dependency-version) below
- Enable pre-commit support via [`pre-commit-hooks.json5`](#pre-commit-hooks) below
- Separate minor and patch version updates for Python and MariaDB
- Combine updates for
  - the `markdownlint-cli2` image and pre-commit hook
  - the `mkdocs-material` image and package
  - the `ruff` package and pre-commit hook
  - the `uv` package and pre-commit hook

### [docker-alpine](./presets/docker-alpine.json5)

Use this to ensure that Alpine versions in Docker base images receive updates.
For example, when using `python:3.11.9-alpine3.19` Renovate will update to Alpine 3.20 when such an image is available.
Note that if the base version and Alpine version have an update at the same time, Renovate will only create one update at a time. I.e., first, the Python version is updated, then the Alpine version.

If you want updates for `node` images, you must ignore the preset [`workarounds:nodeDockerVersioning`](https://docs.renovatebot.com/presets-workarounds/#workaroundsnodedockerversioning) in the project's config.

Thanks to [Aaron Goldenthal](https://github.com/renovatebot/renovate/discussions/29501) for help in getting this working.

### [actions-dependency-version](./presets/actions-dependency-version.json5)

Use this to pin a dependency version in GitHub Action workflows.
For example, when using the [`setup-uv`](https://github.com/astral-sh/setup-uv) action you might want to pin the [`uv`](https://docs.astral.sh/uv/) version that is being installed.
And, update this version when `uv` is updated.

With this preset you can do that as follows:

```yaml
- uses: astral-sh/setup-uv@cec208311dfd045dd5311c1add060b2062131d57 # v8.0.0
  with:
    # renovate: datasource=pypi depName=uv
    version: "0.11.2"
```

> [!NOTE]
> You can achieve the same outcome using `_VERSION` environment variables using the custom manager [customManagers:githubActionsVersions](https://docs.renovatebot.com/presets-customManagers/#custommanagersgithubactionsversions) provided by Renovate.

### [pre-commit-hooks](./presets/pre-commit-hooks.json5)

This preset enables updates the [pre-commit manager](https://docs.renovatebot.com/modules/manager/pre-commit/) and groups pre-commit hook updates together.
The hooks are updated on a weekly schedule to reduce PR noise.

> [!NOTE]
> If your base configuration changes the defaults of [`separateMinorPatch`](https://docs.renovatebot.com/configuration-options/#separateminorpatch) you might want to specify other defaults for the "pre-commit hooks" group using an additional package rule.

### Packages

#### [actions-docker](./presets/packages/actions-docker.json5)

Group updates for Docker actions and schedule them weekly.

#### [typos](./presets/packages/typos.json5)

Limit [`typos`](https://github.com/crate-ci/typos) updates to monthly and group all version updates together.
