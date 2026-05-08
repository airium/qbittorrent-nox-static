# CLI Usage Guide

This guide explains all CLI flags and argument modes for this repository.

If you only need a prebuilt binary, use `qi.bash`.
If you want to compile from source (including custom/local sources), use `qbt-nox-static.bash`.

## Which Script Should You Use?

| Goal | Script | Typical Command |
|---|---|---|
| Download and install a precompiled static binary | `qi.bash` | `./qi.bash -lt v2` |
| Build qBittorrent + dependencies from source | `qbt-nox-static.bash` | `./qbt-nox-static.bash all` |
| Legacy build behavior compatibility | `qbittorrent-nox-static.sh` | `./qbittorrent-nox-static.sh all` |

## 1) Precompiled Binary Installer (`qi.bash`)

### Syntax

```bash
./qi.bash [OPTIONS]
```

### Options

| Flag | Argument | Required? | Description |
|---|---|---|---|
| `-lt`, `--libtorrent` | `v1` or `v2` | Optional | Select release line. Default: `v2`. |
| `-fa`, `--force-arch` | architecture | Optional | Override detected architecture (`x86_64`, `x86`, `aarch64`, `armv7`, `armhf`, `riscv64`). |
| `-h`, `--help` | none | Optional | Print help and exit. |

### Environment variable equivalents

| Variable | Value |
|---|---|
| `LIBTORRENT_VERSION` | `v1` or `v2` |
| `FORCE_ARCH` | `x86_64`, `x86`, `aarch64`, `armv7`, `armhf`, `riscv64` |

### Examples

```bash
# Install latest release with libtorrent v2
./qi.bash

# Install latest release with libtorrent v1.2 line
./qi.bash -lt v1

# Force architecture
./qi.bash -fa armv7
```

## 2) Source Build Script (`qbt-nox-static.bash`)

### Syntax

```bash
./qbt-nox-static.bash [FLAGS...] <MODULE...>
```

A build run needs at least one module argument (for example `all`).
If no module is provided, or an invalid module is provided, the script prints supported modules and exits.

### Module arguments

Common module targets:

- `all` (recommended full build path)
- `zlib`
- `openssl`
- `boost`
- `libtorrent`
- `qtbase`
- `qttools`
- `qbittorrent`
- `icu` (optional)
- `iconv` (used by some libtorrent lines)
- `glibc` (Debian/Ubuntu static flow)
- `install` (install already built `completed/qbittorrent-nox`)

Notes:

- Module availability can vary by platform and mode.
- `all` is the normal full-source build entrypoint.

### Build and toolchain flags

| Flag | Argument | Required? | Description |
|---|---|---|---|
| `-b`, `--build-directory` | path | Optional | Build/install prefix directory. Default: `qbt-build` under current working directory. |
| `-c`, `--cmake` | none | Optional | Use CMake flow (default). |
| `-q`, `--qmake` | none | Optional | Use qmake/configure flow (legacy combos). |
| `-d`, `--debug` | none | Optional | Debug build settings, symbols on. |
| `-s`, `--strip` | none | Optional | Strip final binary symbols. |
| `-si`, `--static-ish` | none | Optional | Do not statically link libc (system libc linkage). |
| `-o`, `--optimise` | none | Optional | Enable host-optimized compile flags (`-march=native` behavior). |
| `-cb`, `--cpu-baseline` | value | Optional | Explicit CPU baseline (`-march=value`). |
| `-ct`, `--cpu-tune` | value | Optional | Explicit CPU tune (`-mtune=value`). |
| `-n`, `--no-delete` | none | Optional | Keep intermediate module build/source dirs instead of deleting them. |
| `-i`, `--icu` | none | Optional | Include ICU build/use path. |
| `-ma`, `--multi-arch` | arch | Optional | Cross-build target architecture. |

CPU flag behavior:

- `-cb`/`-ct` take precedence over `-o` where both are used.
- CPU flags do not add runtime multiversion dispatch by themselves.

### Source/version selection flags

| Flag | Argument | Required? | Description |
|---|---|---|---|
| `-bt`, `--boost-tag` | git tag/branch | Optional | Override Boost version source. |
| `-lt`, `--libtorrent-tag` | git tag/branch | Optional | Override libtorrent version source. |
| `-ot`, `--openssl-tag` | git tag/branch | Optional | Override OpenSSL version source. |
| `-qt`, `--qbittorrent-tag` | git tag/branch | Optional | Override qBittorrent version source. |
| `-qtt`, `--qt-tag` | git tag/branch | Optional | Override Qt tag for `qtbase` and `qttools`. |
| `-lm`, `--libtorrent-master` | none | Optional | Use libtorrent RC branch head for selected line. |
| `-qm`, `--qbittorrent-master` | none | Optional | Use qBittorrent `master`. |
| `-m`, `--master` | none | Optional | Use master/RC branch sources for both libtorrent and qBittorrent. |
| `-ls`, `--libtorrent-local-src` | local path | Optional | Use local libtorrent source tree (rsync copy). |
| `-qs`, `--qbittorrent-local-src` | local path | Optional | Use local qBittorrent source tree (rsync copy). |
| `-wf`, `--workflow` | none | Optional | Prefer workflow archive sources where allowed. |
| `-pr`, `--patch-repo` | `owner/repo` | Optional | Fetch patch assets from a custom patch repository. |

Local source mode behavior (`-ls` and/or `-qs`):

- Source is copied locally via `rsync` into build tree.
- Script updates submodules in copied tree.
- Patching for local libtorrent/qBittorrent is skipped.
- Local mode conflicts with remote tag/master controls for the same module.

### Cache/network and diagnostics flags

| Flag | Argument | Required? | Description |
|---|---|---|---|
| `-cd`, `--cache-directory` | path `[rm|bs]` | Optional | Set cache directory for archives/repos. Optional mode: `rm` remove cache and exit, `bs` bootstrap cache and exit. |
| `-p`, `--proxy` | proxy URL | Optional | Configure curl/git proxy for downloads and git operations. |
| `-sdu`, `--script-debug-urls` | none | Optional | Print resolved URL/tag arrays and exit (debug output). |

### Bootstrap/support flags

| Flag | Argument | Required? | Description |
|---|---|---|---|
| `-bs-e`, `--bootstrap-env` | none | Optional | Write template `.qbt_env` and exit. |
| `-bs-ef`, `--bootstrap-env-full` | none | Optional | Write populated `.qbt_env` and exit. |
| `-bs-p`, `--bootstrap-patches` | none | Optional | Prepare patch directory structure. |
| `-bs-r`, `--bootstrap-release` | none | Optional | Generate release metadata files (CI-oriented). |
| `-bs-ma`, `--bootstrap-multi-arch` | none | Optional | Bootstrap cross-build helper assets (CI/cross flow). |
| `-bs-a`, `--bootstrap-all` | none | Optional | Run all bootstrap helper steps. |
| `-bs-c`, `--bootstrap-cmake` | none | Optional | Bootstrap-related CMake mode toggle. |

### Help flags

| Flag | Argument | Description |
|---|---|---|
| `-h`, `--help` | none | Main help listing. |
| `-h-*` variants | none | Topic-specific help for each primary flag (for example `-h-cd`, `-h-lt`, `-h-qs`). |

### Positional maintenance commands (non-module utility commands)

These are not normal module build targets but are recognized by dependency/setup logic:

- `update`
- `install_test`
- `install_core`
- `bootstrap_deps`
- `debug`
- `bootstrap`

### Important constraints and conflicts

- Local source conflicts:
  - `-ls` conflicts with `-lm` and `-lt` for libtorrent.
  - `-qs` conflicts with `-qm` and `-qt` for qBittorrent.
  - `-m` conflicts with local source mode.
- `-o` and `-si` are native-mode only (not cross compile mode).
- Qt/build-tool compatibility rules are enforced (qmake vs cmake combinations).

## Environment Variables

All `qbt_*` variables can be set as exported environment variables before running the script, or placed in a `.qbt_env` file in the same directory as the script. CLI flags take highest priority and override both `.qbt_env` and exported variables.

**Precedence order**: CLI flags > `.qbt_env` file > exported environment variables.

Use `-bs-e` to create a template `.qbt_env` with empty values, or `-bs-ef` to create one populated with current defaults.

### Build configuration

| Variable | Default | Description |
|---|---|---|
| `qbt_build_dir` | `qbt-build` | Build/install directory name, relative to working directory. Set via `-b`. |
| `qbt_build_tool` | `cmake` | Build tool: `cmake` (Qt6) or `qmake` (Qt5). Set via `-c`/`-q`. |
| `qbt_build_debug` | `no` | `yes` for a full debug build (debug symbols, no stripping, no LTO). Set via `-d`. |
| `qbt_standard` | `17` | C++ standard baseline (`14`, `17`, `20`, `23`). Dynamically raised by the script based on app versions. |
| `qbt_qt_version` | Derived from build tool (`6` for cmake, `5` for qmake) | Qt major version: `5` or `6`. Can be set to build with a specific Qt line. |

### Optimization and linking

| Variable | Default | Description |
|---|---|---|
| `qbt_optimise` | `no` | `yes` to add `-march=native` to build flags. Native builds only, ignored for cross builds. Set via `-o`. |
| `qbt_optimise_strip` | `yes` | `yes` to strip symbols from the final binary. `no` keeps debug symbols in a release build. Set via `-s`. |
| `qbt_use_lto` | Auto-detected (`yes` on Alpine native and Debian/Ubuntu, `no` otherwise) | Link Time Optimization. Not used in cross builds. Can be forced to `no` to disable. On mixed static/dynamic builds, disabling LTO can prevent runtime crashes. |
| `qbt_linker_mold` | `no` | `yes` to use the mold linker (`-fuse-ld=mold`). Requires qbt-mcm 2614 or newer. |
| `qbt_static_ish` | `no` | `yes` to skip static linking of glibc (uses system glibc instead). Only for Debian/Ubuntu. Not compatible with cross compilation. Set via `-si`. |

### CPU tuning

| Variable | Default | Description |
|---|---|---|
| `qbt_cpu_baseline` | *(empty)* | Explicit CPU baseline for `-march` (e.g., `x86-64-v2`). Empty auto-detects. Set via `-cb`. Takes precedence over `-o`. |
| `qbt_cpu_tune` | *(empty)* | Explicit CPU tuning for `-mtune` (e.g., `haswell`). Empty auto-detects. Set via `-ct`. Takes precedence over `-o`. |

### Version and tag overrides

| Variable | Default | Description |
|---|---|---|
| `qbt_libtorrent_version` | `2.0` | Libtorrent major version line: `1.2`, `2.0`, or `2.1`. Determines default tags and Boost requirements. Set via `-lt` with a version prefix. |
| `qbt_libtorrent_tag` | *(empty → latest)* | Override libtorrent git tag or branch (e.g., `v2.0.12`, `RC_2_0`). Set via `-lt`. |
| `qbt_libtorrent_master_jamfile` | `no` | `yes` to use the RC branch Jamfile instead of the release Jamfile. Can break builds when non-backported changes are present. |
| `qbt_qbittorrent_tag` | *(empty → latest)* | Override qBittorrent git tag or branch. Set via `-qt`. |
| `qbt_qt_tag` | *(empty → latest)* | Override Qt git tag or branch for `qtbase` and `qttools`. Set via `-qtt`. |
| `qbt_openssl_tag` | *(empty → latest)* | Override OpenSSL git tag or branch. Set via `-ot`. |
| `qbt_openssl_lts` | `3.5` | OpenSSL LTS version line. Used when no explicit tag is set to select the latest patch release of that line. |
| `qbt_boost_tag` | `boost-1.86.0` for libtorrent 1.2; *(empty → latest)* for 2.0+ | Override Boost git tag. Set via `-bt`. Only needed for libtorrent 1.2 builds. |

### Local source overrides

| Variable | Default | Description |
|---|---|---|
| `qbt_libtorrent_local_src` | *(empty)* | Path to a local libtorrent source directory. If set, used instead of downloading. Patching is skipped for local sources. Set via `-ls`. |
| `qbt_qbittorrent_local_src` | *(empty)* | Path to a local qBittorrent source directory. If set, used instead of downloading. Patching is skipped for local sources. Set via `-qs`. |

### Dependency management

| Variable | Default | Description |
|---|---|---|
| `qbt_skip_icu` | `yes` | `yes` to skip building ICU. Set to `no` to enable ICU (or use `-i`). Skipped by default because most builds do not need it. |
| `qbt_zlib_type` | `zlib` | Which zlib implementation: `zlib` (standard) or `zlib-ng`. |
| `qbt_host_deps` | `no` | `yes` to use prebuilt host dependency packages from GitHub. Useful for cross builds without QEMU. |
| `qbt_host_deps_repo` | `userdocs/qbt-host-deps` | GitHub `username/repo` hosting prebuilt host dependency packages. |
| `qbt_with_qemu` | `yes` | `yes` to expect QEMU for cross builds. Controls host dependency and build module behavior. |

### Cross-compilation

| Variable | Default | Description |
|---|---|---|
| `qbt_cross_name` | `default` | Cross-compilation target architecture (e.g., `aarch64`, `armhf`, `armv7`, `x86`, `riscv64`, `loongarch64`). `default` = native build. Set via `-ma`. |
| `qbt_cross_target` | Host OS ID | OS target for cross builds. Defaults to the host operating system identifier. |
| `QBT_MCM_DOCKER` | *(empty)* | Set to `YES` inside an MCM Docker cross-build container. Tells the script the host is a cross-build environment, not a native one. |
| `QBT_MCM_TARGET` | *(empty)* | MCM target identifier for Docker cross-build containers. |
| `QBT_CROSS_NAME` | *(empty)* | Uppercase variant that overrides `qbt_cross_name` if set. Used by CI and Docker workflows. |

### Network and source configuration

| Variable | Default | Description |
|---|---|---|
| `qbt_git_proxy` | *(empty)* | Proxy URL for all git operations. Set via `-p`. |
| `qbt_curl_proxy` | *(empty)* | Proxy URL for all curl/download operations. Set via `-p`. |
| `qbt_patches_url` | `userdocs/qbittorrent-nox-static` | GitHub `username/repo` to fetch patches from. The repo must follow the structure `/patches/<module>/<version>/patch`. Set via `-pr`. |
| `qbt_mcm_url` | `userdocs/qbt-musl-cross-make` | GitHub `username/repo` for musl-cross-make toolchain builds. |
| `qbt_mcm_tag` | *(empty → latest)* | Specific release tag for qbt-musl-cross-make downloads. |
| `qbt_revision_url` | `userdocs/qbittorrent-nox-static` | GitHub repo for build revision files (CI-specific). |
| `qbt_workflow_files` | `no` | `yes` to use prebuilt archives from the `qbt-workflow-files` repo instead of building dependencies from source. Set via `-wf`. |

### Script behavior

| Variable | Default | Description |
|---|---|---|
| `qbt_legacy_mode` | `no` | `yes` to make the script behave like the old `qbittorrent-nox-static.sh` — auto-installs core deps if privileges allow. |
| `qbt_advanced_view` | `yes` | `no` to hide advanced options from the config summary. Useful for simpler first-run output. |
| `qbt_skip_delete` | *(empty)* | `yes` to keep intermediate build/source directories after the build instead of deleting them. Set via `-n`. |

### Compiler and linker flags

These variables are consumed from the standard environment and appended to the script's own build flags. Set them as exported environment variables or in `.qbt_env`:

| Variable | Description |
|---|---|
| `CFLAGS` | C compiler flags. Consumed once and stored internally. |
| `CXXFLAGS` | C++ compiler flags. Consumed once and stored internally. |
| `CPPFLAGS` | C preprocessor flags. Consumed once and stored internally. |
| `LDFLAGS` | Linker flags. Consumed once and stored internally. |

### Example: environment variable usage

```bash
# Equivalent to: ./qbt-nox-static.bash -si -i -ot openssl-3.5.6 -ct ivybridge all
qbt_static_ish="yes" qbt_skip_icu="no" qbt_openssl_tag="openssl-3.5.6" qbt_cpu_tune="ivybridge" ./qbt-nox-static.bash all

# Disable LTO for mixed static/dynamic builds (prevents potential runtime crashes)
qbt_use_lto="no" ./qbt-nox-static.bash -si all
```

## Practical command recipes

### Full source build (default CMake flow)

```bash
./qbt-nox-static.bash all
```

### Full build with custom build and cache directories

```bash
./qbt-nox-static.bash -b ~/build -cd ~/tmp all
```

### Local dev build for libtorrent + qBittorrent sources

```bash
./qbt-nox-static.bash \
  -ls ~/src/libtorrent-rasterbar -qs ~/src/qBittorrent \
  -cb x86-64-v2 -ct x86-64-v3 \
  -b ~/build -cd ~/tmp \
  -o all
```

### Cache bootstrap only (download/cache then exit)

```bash
./qbt-nox-static.bash -cd ~/tmp bs all
```

### Build only selected modules

```bash
./qbt-nox-static.bash openssl boost libtorrent
```

### Static-ish build on Debian/Ubuntu (portable across OS versions)

```bash
qbt_use_lto="no" ./qbt-nox-static.bash \
  -ls ~/src/libtorrent-rasterbar -qs ~/src/qbittorrent \
  -cb ivybridge -ct ivybridge \
  -b ~/build -cd ~/cache \
  -i -ot openssl-3.6.2 \
  -o all
```

Notes for static-ish (`-si`) builds:

- `qbt_use_lto="no"` is recommended to avoid potential runtime crashes from LTO with mixed static/dynamic linking.
- `-i` builds ICU from source. Without it, Qt6 may auto-detect and dynamically link system ICU, creating a runtime dependency on the build host's ICU version.
- If changing dependency versions via flags (e.g., `-ot`), purge the corresponding cache entry first: `rm -rf ~/cache/<module>`.

## Legacy script (`qbittorrent-nox-static.sh`)

`qbittorrent-nox-static.sh` is the legacy-compatible variant. Most flags are the same as `qbt-nox-static.bash`, but defaults and behavior for some compatibility modes can differ.

For new setups, prefer `qbt-nox-static.bash` unless you specifically need legacy behavior.
