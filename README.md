<div align="center">

![license](https://img.shields.io/github/license/seaofvoices/release-luau-package-action)

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/seaofvoices)

</div>

# Release Luau Package Action

A GitHub composite action to publish and release Sea of Voices Luau packages with built assets attached to a GitHub release. It will:

- publish to npm (using `yarn` or `npm`) using [trusted publishing](https://docs.npmjs.com/trusted-publishers)
- create a new tag
- create a new release
- attach assets to the release

This action depends on the [test-luau-package action](https://github.com/seaofvoices/test-luau-package-action) to install and build assets for release.

## Usage

```yaml
on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to release (e.g., v1.0.0)'
        required: false
        type: string

jobs:
  release:
    runs-on: ubuntu-latest

    permissions:
      contents: write
      id-token: write

    steps:
      - uses: seaofvoices/release-luau-package-action@v1
        with:
          release-tag: ${{ inputs.version }}
          package-manager: yarn
          artifacts: >-
            [
              {"name":"disk.rbxm","path":"build/disk.rbxm","content-type":"application/octet-stream"},
              {"name":"disk.luau","path":"build/disk.luau","content-type":"text/plain"}
            ]
```

Each artifact object should include:

| Key | Meaning |
|-----|---------|
| `name` | Name of the file on the release (for example `roblox-model.rbxm`) |
| `path` | Path to the built file relative to `working-directory` |
| `content-type` | `Content-Type` for the upload (for example `application/octet-stream`) |

The action will:
3. Publish to npm with provenance
4. Create and push a git tag (if it doesn't exist)
5. Create a GitHub release
6. Upload all specified artifacts to the release

### Permissions

The job that runs this action needs permissions to:
- Create and push tags
- Create releases and upload assets
- Publish to npm with provenance

```yaml
permissions:
  contents: write
  id-token: write
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `artifacts` | JSON array of objects with keys `name`, `path`, and `content-type` | Yes | `[]` |
| `release-tag` | The version to release starting with `v` (e.g., `v1.0.0`) | No | Defaults to the `version` of the `package.json` |
| `package-manager` | `yarn` or `npm` | No | `yarn` |
| `working-directory` | Directory for all steps, relative to the repository root | No | `.` |
| `release-ref` | The branch, tag or SHA to checkout (empty uses the default ref) | No | - |
| `github-token` | GitHub token for authentication | No | - |

## License

This project is available under the MIT license. See [LICENSE.txt](LICENSE.txt) for details.
