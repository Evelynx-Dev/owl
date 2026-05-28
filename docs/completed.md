# Completed Features

All completed features and improvements for Owl.

---

## v0.13.x — Tests, Profile & Import Mode (2026-05-25)

- `owl test` re-enabled with native test runner (`tests/*.mire` discovery + `--filter`).
- `owl profile` re-enabled (cache/check history, JSON output, compare mode).
- `--import-mode <legacy|reachable>` flag in `run/build` with automatic fallback.
- Run cache key hardened: `source_hash + profile + opt + compiler + import_mode`.
- `run` fast-path also works when passing runtime args (`-- ...`).
- Dependency preflight in project mode: validates lock entries for `semver` + `sha256:`.
- `run/build` unified into shared compile pipeline.
- Compiler command resolution: `owl.toml` → `[compiler].cmd` > `[build].compiler` > `PATH`.
- `owl profile --json` returns proper JSON object output.

## v0.12.0 — Installer (2026-05-18)

- `scripts/install.sh` (automatic) and `scripts/install-review.sh` (auditable).
- Precompiled binaries from GitHub Releases: Linux/macOS, x86_64/aarch64.
- Configurable: `--prefix`, `--bin-dir`, `--with-mire`, `--owl-repo`, `--mire-repo`.
- Release asset naming convention documented.

## v0.11.0 — Check & Profile (2026-05-18)

- `owl check` with `--all`, `--strict`, `--deny Wxxxx`.
- `owl profile` with `--json`, `--history`, `--compare`.
- `modules/diagnostics.mire` and `modules/profile.mire`.
- `run/build` accept `-r` and `-d` short flags.
- `clean` selective cleanup: `--cache`, `--bin`, `--all`/`-A`.
- `info` redesigned with diagnostics-style layout.

## v0.10.0 — Local-Only Redesign (2026-05-18)

- Removed external dependency management (install, remove, update, purge, deps).
- Eliminated modules: `deps`, `registry`, `download`, `lock`, `semver`.
- Core commands: `new`, `run`, `build`, `test`, `clean`, `info`.
- Module paths integration: `~/.owl/modules/` and `project_root/modules/`.
- `install.sh` creates `~/.owl/{modules,tmp,config.toml}`.

## v0.6.0 — Full Package Manager (Legacy, removed in v0.10.0)

### Phase 5 — Symlinks + Build Wrapper
- `create_symlink(name, version)` in `deps.mire` — creates `project/deps/<name>` → `~/.owl/deps/<name>/<version>/code/`
- `verify_build_deps()` in `deps.mire` — reads `owl.lock`, verifies/creates symlinks for all deps
- `cmd_build()` in `main.mire` — reads entry from `owl.toml`, verifies deps, lists locked dependencies, runs `mire build <entry>`
- `cmd_run()` in `main.mire` — same flow + runs binary on success

### Phase 6 — SemVer Update Policy
- `semver_diff(a, b)` in `semver.mire` — determines "patch", "minor", or "major" difference
- `update_with_policy(name, url, auto_yes, allow_major)` in `deps.mire`:
  - Patch: auto-update without prompt
  - Minor: confirmation prompt unless `--yes`
  - Major: requires `--major` flag + confirmation prompt unless `--yes`
- `--yes` flag: auto-confirms all minor updates
- `--major` flag: allows major version updates
- Integrated into `cmd_update` and `cmd_update_all` in `main.mire`

### Phase 7 — Security Levels
- `security_warning(url)` in `deps.mire`:
  - `https://github.com/mire-lang/*` — trusted, no warning
  - External `git:`/`http:` URLs — single warning
  - Integrated into `install_dep()` and `install_from_registry()`

### Phase 8 — Package Validation
- `validate_package(dest, name, version)` in `deps.mire`:
  - Reads `owl.toml` from extracted package
  - Validates `project.name` and `project.version` match
  - Validates `project.entry` exists
  - On failure: package directory is removed (atomic install)

---

## v0.5.0 — Module Infrastructure

### Registry System (Phase 1)
- `modules/registry.mire` (437 lines):
  - `registry_sync()` — `git clone --depth 1` to `~/.owl/registry/`
  - `registry_lookup(name, range)` — find best version in index.toml with semver matching
  - `registry_lookup_exact(name, version)` — exact version lookup
  - `registry_list_versions(name)` — all available versions
  - `parse_index()` — parses `index.toml` `[[package]]` entries
  - Semver matching: caret `^`, tilde `~`, comparison ops (`>=`, `>`, `<=`, `<`, `=`)

### Tarball Download (Phase 2)
- `modules/download.mire` (65 lines):
  - `download_tarball(url, checksum, dest)` — curl + sha256 verify + tar extract
  - `file_sha256(path)` — compute file checksum
  - `ensure_tmp()` — ensure `~/.owl/tmp/` exists

### Lock File (Phase 3)
- `modules/lock.mire` (179 lines):
  - Format: `name|version|url|checksum|deps`
  - `lock_get(name)` — lookup entry by name
  - `lock_get_all()` — read entire lock file
  - `lock_set(name, version, source, sha256, deps)` — upsert entry
  - `lock_remove(name)` — remove entry
  - `lock_entries()` — formatted list of entries
  - `lock_validate(deps_list)` — verify all deps are in lock file

### Dependencies Management (Phase 4)
- `modules/deps.mire` (447 lines):
  - `install_dep(name, url, range)` — dispatches to registry or git
  - `install_from_registry(name, range)` — full flow: sync → lookup → download → lock → recursive deps
  - `install_fresh(name, url, range)` — git-based fresh install
  - `update_existing(name, url, range)` — git fetch + tag resolution + checkout
  - `update_dep(name, url, range)` — public update entry point
  - `install_all_from_toml()` — batch install from `owl.toml`
  - `remove_dep(name)` / `remove_dep_force(name)` — with usage scan
  - `list_deps()` — list installed dependencies
  - `dep_path(name)` / `dep_path_v(name, version)` — path helpers

### CLI Integration
- `code/main.mire` (375 lines) — all commands:
  - `new`, `build`, `run`, `info`, `clean`, `sync`, `test`
  - `install <name> [--url]`, `update [name]`, `remove <name> [--force]`
  - `list`, `version`, `help`
- `owl sync → registry_sync()` connected and functional

### Documentation
- `owl/docs/changelog.md` — full release history
- `owl/docs/issues.md` — open and resolved issue tracker
- `owl/docs/roadmap.md` — phased development plan
- `owl/docs/registry.md` — registry protocol spec
- `owl/docs/technical.md` — architecture notes

---

## v0.4.0 — Functional Dependencies (2026-05-10)

- `owl list`: list installed dependencies with versions
- `owl install <name>`: install from owl.toml or via `--url`
- `owl install`: install all dependencies from owl.toml
- `owl update <name>` / `owl update`: update dependencies
- `owl remove <name>` / `--force`: remove with usage check
- `owl info`: project info with locked dependencies
- Semver: best version resolution from git tags
- Lock file: `owl.lock` with pipe-delimited format
- Global storage in `~/.owl/deps/`

### Fixed
- Mire ownership errors in if/else branches
- Use-after-move in clone_dep, remove_dep, cmd_new, cmd_purge
- main.mire compiles with all modules

## v0.3.1 — Test Fixes (2026-05-10)

- `owl test`: harness no longer overwrites imports
- `owl test`: function name corruption in harness fixed
- `mkdir_p`: handles absolute paths
- `--filter` flag requires value validation
- Help text updated

## v0.3.0 — Complete CLI (2026-05-09)

- Full CLI: new, run, compile, install, purge, deps, test, info, clean, version, help
- Modular organization under `modules/`

## v0.1.0 — Base (2026-05-07)

- Initial project structure
- `owl.toml` support and initial CLI
