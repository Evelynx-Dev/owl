# Owl v0.31.1

Package and project manager for the [Mire](https://github.com/mire-lang) (Avenys) language.
Written in Mire, compiled by Avenys.

Owl provides project scaffolding, compilation orchestration, static analysis,
test execution, package management, and build profiling.

**Always use `owl` CLI. Never use `mire` CLI directly except for compiler development.**

## Quick Start

```bash
owl new myproject
cd myproject
owl run
```

## Commands

### Build

| Short | Command | Description |
|-------|---------|-------------|
| `-B` | `build [file] [-d\|-r] [-O <n>]` | Compile project to binary |
| `-R` | `run [file] [-d\|-r] [-O <n>] [-- <args>]` | Compile and execute |
| `-T` | `test [file] [--verbose] [--no-run]` | Run test suite |
| `-K` | `check` | Validate dependencies (path, version, integrity) |
| `-D` | `debug [file] [--tokens\|-t] [--ast\|-p] [--ir] [--run\|-r]` | Compiler introspection |
| `-Q` | `info [--json]` | Project and environment information |

### Project

| Short | Command | Description |
|-------|---------|-------------|
| `-N` | `new <Name>` | Scaffold a new project |
| `-C` | `clean [--bin] [--cache] [--all\|-A] [--global]` | Remove build artifacts and cache |
| | `checkup [--fix <field>...]` | Project diagnostics and repair |
| | `tree [--all]` | Show dependency tree |
| | `profile [--json]` | Build metrics |

### Packages

| Short | Command | Description |
|-------|---------|-------------|
| `-L` | `load <name>` | Add dependency to owl.toml |
| `-L` | `load -Lu <url>` | Add a package registry |
| `-L` | `load -Ll` | List registries |
| `-L` | `load -Ls` | Sync registries |
| `-L` | `load -Lr <name>` | Remove registry |
| `-S` | `install <name> [ver]` | Download and install package |
| `-S` | `install --lock` | Install all packages from owl.lock |
| `-S` | `install -l` | List packages from all registries |
| | `install --prune` | Install and prune unused dependencies |
| | `deps --prune` | Remove unused dependencies from owl.toml |
| `-e` | `export [--check\|--dry-run]` | Package and sign |
| | `gc` | Garbage collect orphaned packages |
| | `upgrade [--yes]` | Self-update owl from source |

### Global

| Flag | Description |
|------|-------------|
| `-V`, `--version` | Show version |
| `-h`, `--help` | Show help |

## checkup Command — Diagnostics & Repair

```bash
# Full diagnostic (all checks)
owl checkup

# Specific checks
owl checkup --cache       # validate build cache integrity
owl checkup --deps        # validate dependencies can be loaded
owl checkup --loads       # validate load statements resolve

# Repair (skips diagnostics when --fix with fields specified)
owl checkup --fix loads        # scan sources, inject missing deps from load statements
owl checkup --fix deps         # resolve dep paths from ~/.owl/libs
owl checkup --fix cache        # clean build cache
owl checkup --fix name entry   # regenerate owl.toml fields

# Example: fix missing dependencies from load statements
owl checkup --fix loads
```

### checkup Behavior

- **Without `--fix`**: runs all selected diagnostics, reports all issues
- **With `--fix <fields>`**: **skips diagnostics**, runs only the requested fix
  - `--fix loads` → scans source files for `load` statements, injects missing deps
  - `--fix deps` → resolves dep paths from `~/.owl/libs/`
  - `--fix cache` → clears `bin/.cache`
  - `--fix <field>` → regenerates `owl.toml` fields

---

## Module Loading — Important Rules

### External packages (from `[dependencies]`)

```mire
# In code/main.mire
load kioto              # makes kioto namespace available
load blu::parse         # specific submodule from blu
load sdl::sdl3          # submodule from sdl

# Usage requires use!
pub fn main: () {
    use! kioto::strings::concat("a" "b")
    use! blu::parse::load_file("style.css")
    use! sdl::sdl3::create_window("title" 800 600)
}
```

### Redundant Load Anti-pattern

```mire
# WRONG — redundant
load blu
load blu::parse
load blu::widget

# CORRECT — load blu once, it exposes everything
load blu

pub fn main: () {
    use! blu::parse::load_file("style.css")
    use! blu::widget::arena::create()
}
```

**Why:** `load blu` imports the entire `blu` package namespace. Submodules like `blu::parse`, `blu::widget` are accessible as `blu::parse::...` and `blu::widget::...`. Loading them again is redundant.

### Local modules (within project)

```mire
# In code/main.mire
load! code/lib/utils    # local module from code/lib/utils/mod.mire

# Usage requires use!
pub fn main: () {
    use! utils::helper()
}
```

### Key Loading Rules

| Rule | Description |
|------|-------------|
| `load X` | External package from `[dependencies]`. **Must be in owl.toml**. |
| `load! X` | Local module (`code/...`). Path relative to `sources` dir. |
| `use! mod::fn()` | **Mandatory** for ALL cross-module calls. |
| `load X::Y` | Submodule of external package. |
| `load! X::Y` | NOT valid — local modules loaded as single unit. |

---

## Build profiles

```bash
owl build --release -O3   # Release mode, max optimization
owl run -r -Os            # Release, size optimization
owl check                 # Validate dependencies
```

## Project structure

```
myproject/
  owl.toml          # Project manifest
  code/main.mire    # Entry point
  tests/            # Test files
  bin/
    debug/          # Debug binaries
    release/        # Release binaries
    .cache/         # Build cache
```

## owl.toml

```toml
[project]
name = "myproject"
version = "0.1.0"
description = ""
entry = "code/main.mire"

[build]
compiler = "mire"
profile = "debug"
opt-level = "0"

[paths]
sources = "code"
tests = "tests"
output = "bin"
cache = "bin/.cache"

[dependencies]
kioto = { path = "~/.owl/libs/kioto", version = "2.4.7" }
```

**Critical:** All external packages MUST be in `[dependencies]` for `load` to work.

## Lockfile

Owl generates `owl.lock` automatically when you build or install. The lockfile
pins exact versions and paths for reproducible installs.

```bash
owl install --lock   # Install all packages from owl.lock
```

## Documentation

- [Changelog](docs/changelog.md) — release history
- [Technical notes](docs/technical.md) — architecture overview

## License

GNU General Public License v3.0