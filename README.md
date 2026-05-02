# trussc-actions

Reusable GitHub Actions workflows for TrussC CI. Builds addons across macOS, Windows, and Linux.

## Usage

### Addon

```yaml
jobs:
  build:
    uses: 2bbb/trussc-actions/.github/workflows/build-addon.yml@v1
    with:
      addon_name: "tcxMyAddon"
```

### With Test App

```yaml
jobs:
  build:
    uses: 2bbb/trussc-actions/.github/workflows/build-addon.yml@v1
    with:
      addon_name: "tcxMyAddon"
      test_app: "testApp"
      test_mode: "test"
      configs: '["Debug", "Release"]'
```

### With Linux Dependencies

```yaml
jobs:
  build:
    uses: 2bbb/trussc-actions/.github/workflows/build-addon.yml@v1
    with:
      addon_name: "tcxMyAddon"
      extra_apt_packages: "libdrm-dev libgbm-dev libegl-dev"
```

## Inputs

### build-addon.yml

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `addon_name` | ✅ | — | Directory name under `addons/` |
| `trussc_ref` | | `main` | TrussC ref (branch/tag/commit) |
| `test_app` | | `""` | Test app directory (empty = auto-generate minimal project) |
| `test_mode` | | `run` | `build-only` / `run` / `test` |
| `configs` | | `'["Release"]'` | JSON array of build configs |
| `preprocessor_defines` | | `""` | Windows preprocessor defines (semicolon-separated) |
| `extra_apt_packages` | | `""` | Extra Linux apt packages (space-separated) |
| `cache_key_suffix` | | `v1` | Cache key suffix for busting |
| `submodules` | | `true` | Checkout submodules recursively |

## test_mode

| Mode | Behavior |
|------|----------|
| `build-only` | Compile only, don't execute |
| `run` | Compile and execute, tolerate non-zero exit codes, **but crash (segfault etc.) fails CI** |
| `test` | Compile and execute, **fail CI on any non-zero exit code** |

All modes detect crashes (segfault, abort, etc.) and fail the CI run. If the test binary is not found, CI also fails.

## How It Works

1. Clones TrussC from GitHub at specified ref
2. Builds `trusscli` (cached between runs)
3. Links addon into `TrussC/addons/`
4. Uses `trusscli update` to generate CMake project
5. Builds via CMake
6. Optionally runs the test app

## License

MIT
