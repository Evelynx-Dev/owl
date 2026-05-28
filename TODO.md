# Owl TODO

## Kioto — Biblioteca estándar oficial de Mire

Plan completo en `../mire-docs/kioto.md`.

### P0 — Wrappers incompletos (std/runtime existe, falta wrapper)

- [ ] `kioto::time`: `unix_ms()`, `unix_ns()`, `sleep_ms()` + ABI C
- [ ] `kioto::mem`: `used()`, `total()`, `percent()`, `snapshot()`
- [ ] `kioto::cpu`: `count()`, `freq_mhz()`, `loadavg()`, `snapshot()`
- [ ] `kioto::math` (builtins): `sin`, `cos`, `tan`, `sqrt`, `pow`, `log`, `log10`, `exp`, `atan2`, `asin`, `acos`
- [ ] `kioto::lists` (funcional): `map`, `filter`, `fold`, `reduce`, `find`, `any`, `all`, `chunk`, `zip`, `flatten`, `reverse`, `sort`, `unique`, `slice`
- [ ] `kioto::dicts` (funcional): `merge`, `invert`, `filter`, `map_values`, `entries`
- [ ] `kioto::term` (real): ANSI escapes para `style`, `clear` real

### P0.5 — C Runtime ABI

- [ ] `__kioto_env_chdir`, `__kioto_time_unix_ms`, `__kioto_time_unix_ns`
- [ ] `__kioto_time_sleep_ms`, `__kioto_cpu_count`, `__kioto_cpu_freq_mhz`
- [ ] `__kioto_io_print`, `__kioto_io_print_err`, `__kioto_io_readln`

### P1 — Foundation (bugs/deuda técnica)

- [ ] `maybe/result`: `unwrap` no debe retornar `mu` (type-safe)
- [ ] `maybe`: `map` debe recibir callback, no valor directo
- [ ] `result`: añadir `map` para success value
- [ ] P1 modules: no usar builtins (`dicts.get`, `lists.get`) directamente

### P2+ — Futuro

- [ ] `kioto::platform`, `kioto::path`, `kioto::net`, `kioto::crypto`
- [ ] `kioto::thread`, `kioto::sync`, `kioto::gpu`

## Owl CLI

### Pending (v0.14.0)

- [ ] Modularizar `code/main.mire` (~974 líneas):
  - `code/project.mire` — helpers de proyecto (`has_project`, `project_entry`/`name`/`version`/`profile`/`import_mode`, etc.)
  - `code/profile.mire` — perfilado y logging (`profile_show`, `profile_record`, `check_print_warning_summary`, etc.)
  - `code/deps.mire` — gestión de dependencias y lock (`verify_build_deps`, `lock_*`, `semver_valid`, `checksum_valid`)
  - `code/flags.mire` — parseo de flags (`parse_file_and_flags`, `parse_check_flags`, `norm_opt`)
  - `code/build.mire` — pipeline de compilación (`compile_pipeline`, `build_cmd`, `proc_run_build_compat`, caché)
  - `code/fs_helpers.mire` — utilidades de sistema de archivos (`safe_size`, `test_collect_files`, `ensure_runtime_dirs`, `source_hash`, etc.)
  - `code/cmd_*.mire` — un archivo por comando (opcional, o mantener en `code/commands.mire`)
- [ ] `--json` output for CI/IDE tooling
- [ ] Cache management extensions
- [ ] Profile persistence in project config
- [ ] External dependency management v2 (basado en lock preflight)

### Done (v0.13.x)

- `owl test` with native runner + `--filter`.
- `owl profile` with JSON/history/compare.
- `--import-mode` flag with fallback.
- Run cache key: hash+profile+opt+compiler+import-mode.
- Dependency preflight: semver + sha256: validation.
- Compiler command from `[compiler].cmd`.

### Done (v0.12.x)

- `scripts/install.sh` and `install-review.sh`.
- Precompiled binaries from GitHub Releases.

### Done (v0.11.x)

- `owl check`, `owl profile` commands.
- Short flags `-r`/`-d` for release/debug.
- `clean` with selective flags.

### Done (v0.10.0)

- Removed external dep management (local-only).
- Module paths: `~/.owl/modules/` + `project_root/modules/`.
- `install.sh` creates `~/.owl/{modules,tmp,config.toml}`.
