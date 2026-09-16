# Owl Changelog

## [1.1.0] - 2026-09-16

### Lockfile V3 Automatic Generation
- `owl ensure` now returns `true` immediately if no `owl.lock` exists
- Project new → lockfile V3 automatically generated on first `build`/`run`
- Lockfile format: `manifest-sha512`, `[[package]]` entries with name/version/path/registry/compiler/language
- V1 and V2 still readable and editable via `owl.toml [owl@lock] version`

### Configuration System `[cfg]`
- New `[cfg]` section in `owl.toml` with `lock` and `update@lock` fields
- `lock = 1` enables lockfile verification, `lock = 0` disables it
- `update@lock` modes: `Build` (always on build), `Changes` (only on owl.toml/load changes), `None` (never auto-update)
- Default: `lock = 1`, `update@lock = "Build"` for new projects
- `owl cfg` command family for managing configuration (planned for 1.2.0)

### Dependency Checksums V3
- `dependency_checksum(pkg_name installed_version)`: Verifies SHA-512 checksums
- Checks `~/.owl/cache/lockfile_checksums` for stored checksums
- Auto-generates and stores missing checksums
- Validates kioto@x.x.x checksums against installed version

### Exports Verification V3
- `exports_verified(pkg_name)`: Validates exported modules from lockfile
- Checks installed package's meta.toml for exports field
- Issues warnings for mismatches or missing format

### Documentation Reorganization
- Docs moved to `owl/docs/` following Mire Documentation Standard
- Each topic has its own subdirectory: `Cli/`, `Lockfile/`, `Reg/`, `Security/`
- `README.md` indexes all documentation for easy navigation
- All documents follow: one level-one heading, no emojis, no decorative HTML

### Breaking Changes (migrated from 1.0.x)
- `proc::run::shell()` removed (was shell violation, use `proc::run::output("cmd", args)`)
- `owl ensure` no longer a standalone command (policy now in build/run/test)
- Lockfile V3 is default; V1/V2 legacy mode only
- `[tool.owl.auto-deps]` removed from `owl new` template

### Compatible Changes
- Old lockfiles (V1, V2) automatically upgraded to V3 on first read
- All existing `owl.toml` configurations continue to work
- Backward compatible with projects created with owl < v1.1.0
- `owl --version` shows real-time: `Owl v1.1.0 / Mire / Avenys v4.0.0`

## [1.0.1] - 2026-08-24
### Transition entry (moved to historical)
- Marks transition from unstructured changelog to formal semantic versioning
- All subsequent entries follow [1.1.0] format
