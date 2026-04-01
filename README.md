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
- uses: astral-sh/setup-uv@v5.2.1
  with:
    # renovate: datasource=pypi depName=uv
    version: "0.5.25"
```

> [!NOTE]
> You can achieve the same outcome using `_VERSION` environment variables using the custom manager [customManagers:githubActionsVersions](https://docs.renovatebot.com/presets-customManagers/#custommanagersgithubactionsversions) provided by Renovate.

### [requires-python](https://github.com/mschoettle/renovate-presets/blob/08df8cf3920b3f17d69ed5f5c33b1845e52f288b/presets/requires-python.json5) (**deprecated**)

Use this preset to pin a Python version in `requires-python` in your `pyproject.toml`.
For example, instead of also specifying the specific Python version in `.python-version` you can be more specific in your `pyproject.toml`:

```toml
[project]
requires-python = "3.13.*"
```

or

```toml
[project]
requires-python = "3.13.2"
```

This preset ensures that the required Python version is updated.

> [!NOTE]
> Renovate can [handle this natively now](https://github.com/renovatebot/renovate/discussions/34793#discussioncomment-12997906).

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
