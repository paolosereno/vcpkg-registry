<!--
SPDX-FileCopyrightText: 2026 Paolo Sereno <pablitos1024@gmail.com>

SPDX-License-Identifier: MIT
-->

# vcpkg registry of paolosereno

A [vcpkg Git registry](https://learn.microsoft.com/vcpkg/maintainers/registries) for
[QtIndustrialWidgets](https://github.com/paolosereno/QtIndustrialWidgets).

| Port | Versions | Verified |
|---|---|---|
| `qtindustrialwidgets` | 2.5.1, 2.5.0#1, 2.5.0 | Linux (`x64-linux-release`), Windows (`x64-windows-static-md-release`, `x64-windows-release`) and macOS (`arm64-osx-release`), by QtIndustrialWidgets' [packaging workflow](https://github.com/paolosereno/QtIndustrialWidgets/blob/main/.github/workflows/packaging.yml) |

## Use

In your project, next to its `vcpkg.json`, a `vcpkg-configuration.json` that takes the port from this registry and
everything else (Qt included) from vcpkg's own registry:

```json
{
  "default-registry": {
    "kind": "builtin",
    "baseline": "<a commit of microsoft/vcpkg>"
  },
  "registries": [
    {
      "kind": "git",
      "repository": "https://github.com/paolosereno/vcpkg-registry",
      "baseline": "<latest commit of this registry>",
      "packages": [ "qtindustrialwidgets" ]
    }
  ]
}
```

The latest commit of this registry is printed by `git ls-remote https://github.com/paolosereno/vcpkg-registry HEAD`.

## Layout

- `ports/<name>/`: the ports (`portfile.cmake`, `vcpkg.json`).
- `versions/baseline.json` and `versions/<x>-/<name>.json`: the version database, written by
  `vcpkg x-add-version <name> --x-builtin-ports-root=./ports --x-builtin-registry-versions-dir=./versions` after the
  port is committed. Each version points to the git tree of its port, so the history of `main` is never rewritten.
- A published version is never changed: a fix to a port is a new `port-version`.

`.github/workflows/verify.yml` checks the version database on every pull request: `vcpkg x-add-version --all` must
change nothing, and every git tree in `versions/` must exist and hold the version its entry declares.
(`vcpkg x-ci-verify-versions` is not used: it wants the dependencies, such as `qtbase`, in this registry's own version
database.)
