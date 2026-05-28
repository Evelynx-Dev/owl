# Issues

Track bugs, feature requests, and tasks for Owl.

For the full development roadmap, see [roadmap.md](roadmap.md).

---

## Template

```markdown
### [ID] Title

**Type**: bug | feature | task
**Priority**: high | medium | low
**Status**: open | in-progress | resolved
**Version**: target version

Description of the issue.

#### Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
```

---

## Open Issues

### [MOD-01] ltrim/rtrim no discriminan izquierda/derecha

**Type**: bug
**Priority**: medium
**Status**: open
**Version**: v0.14.0

`kioto::strings::ltrim` y `rtrim` llaman ambas a `trim` (recortan ambos lados).

### [MOD-02] maybe/result unwrap retorna mu type-unsafe

**Type**: bug
**Priority**: medium
**Status**: open
**Version**: v0.14.0

`maybe::unwrap` y `result::unwrap` retornan `mu` en caso de error, que no es type-safe.

### [MOD-03] term::style es no-op

**Type**: bug
**Priority**: low
**Status**: open
**Version**: v0.14.0

`kioto::term::style` no aplica estilos ANSI. `clear` usa hack de `dasu("\n")`.

### [MOD-04] time module incompleto

**Type**: feature
**Priority**: high
**Status**: open
**Version**: v0.14.0

Faltan `unix_ms()`, `unix_ns()`, `sleep_ms()` y sus ABI C correspondientes.

### [CLI-01] --json output for CI/IDE tooling

**Type**: feature
**Priority**: medium
**Status**: open
**Version**: v0.14.0

Machine-readable output for `owl info`, `owl check`, `owl profile`.

---

## Resolved Issues

### [INT-01] Full Owl compile integration failures (ownership + symbol collisions)

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.9.0

During full `mire build owl/code/main.mire`:
- Several real use-after-move failures were exposed in `modules/deps.mire` and CLI command glue.
- Module-level function collisions caused LLVM redefinitions (`lock`, `toml`, `mkdir_p`).

Fixes:
- Ownership-safe copies in dependency/CLI flows.
- Removed duplicate module imports in `main.mire`.
- Renamed colliding helper in deps module (`mkdir_p` -> `deps_mkdir_p`).

### [OWN-01] Variables consumed in both if/else branches

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.4.0

The Mire compiler cannot track a variable being consumed in both branches of an if/else.
Fix: copy variables for one branch.

### [OWN-02] Use-after-move in clone_dep

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.4.0

`d2` used twice in the `+` chain for the git checkout command in `clone_dep`.

### [OWN-03] Use-after-move in remove_dep / remove_dep_force

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.4.0

`name` was passed to `scan_projects_for_usage(name)` (moves it), then used again.

### [OWN-04] Use-after-move in cmd_new

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.4.0

`name` consumed by `+` in `name + "/code"` then used again in `name + "/docs"`.

### [OWN-05] Use-after-move in cmd_purge

**Type**: bug
**Priority**: medium
**Status**: resolved
**Version**: v0.4.0

`pname` consumed by `+` then used in `set _unused = pname`.

### [CLI-10] --filter requires value validation

**Type**: bug
**Priority**: medium
**Status**: resolved
**Version**: v0.3.1

`owl test --filter` without a value now returns a clear error.

### [TEST-01] Harness import overwrite

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.3.1

Test harness imports were overwriting each other for multiple test files.

### [TEST-02] Function name corruption

**Type**: bug
**Priority**: high
**Status**: resolved
**Version**: v0.3.1

Function name in harness was being corrupted due to string aliasing in the runtime.
