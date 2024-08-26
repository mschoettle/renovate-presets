# renovate-presets

Presets for Renovate Bot to facilitate dependency updates

## Presets

All presets are located in the `presets` directory.

### [docker-alpine](./presets/docker-alpine.json5)

Use this to ensure that Alpine versions in Docker base images receive updates.
For example, when using `python:3.11.9-alpine3.19` Renovate will update to Alpine 3.20 when such an image is available.
Note that if the base version and Alpine version have an update at the same time, Renovate will only create one update at a time. I.e., first, the Python version is updated, then the Alpine version.

If you want updates for `node` images, you must ignore the preset [`workarounds:nodeDockerVersioning`](https://docs.renovatebot.com/presets-workarounds/#workaroundsnodedockerversioning) in the project's config.

Thanks to [Aaron Goldenthal](https://github.com/renovatebot/renovate/discussions/29501) for help in getting this working.
