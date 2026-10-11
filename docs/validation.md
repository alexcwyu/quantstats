# Validation evidence: quantstats

Run date: 2026-10-12. Branch: `feature/ayu_develop`. Interpreter: `3.15.0 (main, Oct  9 2026, 15:11:47) [Clang 22.1.3 ]`.

| Check | Result |
|---|---|
| lock | passed |
| sync | passed |
| compile | passed |
| build | passed |
| tests | passed |

## Current result and compatibility decisions

- The complete supplied suite passes225 cases. The distribution plot uses Matplotlib orientation and explicit box colors rather than Seaborn's deprecated vert argument.
- Matplotlib>=3.10.9,<3.11 avoids Seaborn0.13.2 use of deprecated colormap.set_bad; NumPy>=2.4.6,<2.5 avoids the current Matplotlib date converter's deprecated generic Timedelta unit. These caps are based on actual full-suite failures.

Current unresolved checks:

All supplied checks recorded below passed.

## Latest full-suite output

```text
$ uv run --no-sync python -m pytest
........................................................................ [ 32%]
........................................................................ [ 64%]
........................................................................ [ 96%]
.........                                                                [100%]
225 passed in 66.75s (0:01:06)

```

## Exact commands and exit codes

Unsuccessful rows are retained as historical compatibility attempts; they are not claims that a current passing check remains blocked. Source-build probes use isolated temporary copies to preserve the selected repository environment.

| Command | Exit | Seconds | Evidence |
|---|---:|---:|---|
| `uv lock --upgrade --python 3.15` | 0 | 0.34 | [check record](maintenance/index.html) |
| `uv sync --all-extras --all-groups --python 3.15 --no-build --no-install-project` | 2 | 0.83 | [check record](maintenance/index.html) |
| `uv lock --upgrade --python 3.14` | 0 | 0.29 | [check record](maintenance/index.html) |
| `uv sync --all-extras --all-groups --python 3.14 --no-build --no-install-project` | 0 | 0.55 | [check record](maintenance/index.html) |
| `uv sync --all-extras --all-groups --python 3.14` | 0 | 0.51 | [check record](maintenance/index.html) |
| `uv build --python 3.14` | 0 | 0.92 | [check record](maintenance/index.html) |
| `/Users/alex/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14 -m compileall -q src` | 0 | 0.08 | [check record](maintenance/index.html) |
| `uv run --no-sync python -m pytest` | 1 | 25.68 | [check record](maintenance/index.html) |
| `uv run --no-sync python -m pytest` | 0 | 56.05 | [check record](maintenance/index.html) |
| `uv run --no-sync python -m compileall -q src` | 0 | 0.13 | [check record](maintenance/index.html) |
| `uv build --python 3.14` | 0 | 0.78 | [check record](maintenance/index.html) |
| `uv run --no-sync python -c import sys,importlib.metadata,json; print(sys.version); print(json.dumps({d.metadata["Name"]: d.version for d in importlib.metadata.distributions()},indent=2,sort_keys=True))` | 0 | 0.13 | [check record](maintenance/index.html) |
| `uv sync --directory /tmp/trading-python315-probes/quantstats --all-extras --all-groups --python 3.15` | 0 | 67.54 | [check record](maintenance/index.html) |
| `uv lock --upgrade --python 3.15` | 0 | 0.46 | [check record](maintenance/index.html) |
| `uv sync --all-extras --all-groups --python 3.15` | 0 | 1.87 | [check record](maintenance/index.html) |
| `uv run --no-sync python -m pytest` | 0 | 69.76 | [check record](maintenance/index.html) |
| `uv run --no-sync python -m compileall -q src` | 0 | 0.08 | [check record](maintenance/index.html) |
| `uv build --python 3.15` | 0 | 1.23 | [check record](maintenance/index.html) |
| `uv run --no-sync python -c import sys,importlib.metadata,json; print(sys.version); print(json.dumps({d.metadata["Name"]:d.version for d in importlib.metadata.distributions()},indent=2,sort_keys=True))` | 0 | 0.14 | [check record](maintenance/index.html) |

The versions-final log records the exact interpreter patch release and every installed dependency version. The machine-readable [verification record](maintenance/verification.json) includes branch/default ancestry, status, artifacts and unresolved checks.

## Dependency floor refresh

| Before | After |
|---|---|

The table records the initial metadata refresh. Later compatibility constraints and vendor sources are explained above and the final authoritative requirements are in [pyproject.toml](../pyproject.toml) and [uv.lock](../uv.lock). No test cases were added, removed or disabled. Existing deprecated-API cases retain their assertions while explicitly checking the documented warning.
