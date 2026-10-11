# quantstats: portable verification records

Full raw logs are retained in the workspace maintenance evidence, not distributed here. Hashes identify those original logs. A successful build or selected test subset does not imply a full test-suite pass. Commands containing <workspace> describe the original execution environment.

[Machine-readable records](verification.json) · [Repository validation summary](../validation.md)

## Executed commands

| Check | Exit | Duration | Command | Outcomes |
| --- | ---: | ---: | --- | --- |
| build | 0 | 0.92s | `uv build --python 3.14` | Successfully built dist/quantstats-0.0.86.tar.gz<br>Successfully built dist/quantstats-0.0.86-py3-none-any.whl |
| build-final | 0 | 0.78s | `uv build --python 3.14` | Successfully built dist/quantstats-0.0.86.tar.gz<br>Successfully built dist/quantstats-0.0.86-py3-none-any.whl |
| build-selected-python | 0 | 1.23s | `uv build --python 3.15` | Successfully built dist/quantstats-0.0.86.tar.gz<br>Successfully built dist/quantstats-0.0.86-py3-none-any.whl |
| compile | 0 | 0.08s | `/Users/alex/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14 -m compileall -q src` | See exit status; no test-count assertion. |
| compile-final | 0 | 0.13s | `uv run --no-sync python -m compileall -q src` | See exit status; no test-count assertion. |
| compile-selected-python | 0 | 0.08s | `uv run --no-sync python -m compileall -q src` | See exit status; no test-count assertion. |
| lock-3.14 | 0 | 0.29s | `uv lock --upgrade --python 3.14` | See exit status; no test-count assertion. |
| lock-3.15 | 0 | 0.34s | `uv lock --upgrade --python 3.15` | See exit status; no test-count assertion. |
| lock-python315-source-supported | 0 | 0.46s | `uv lock --upgrade --python 3.15` | See exit status; no test-count assertion. |
| python315-source-probe | 0 | 67.54s | `uv sync --directory /tmp/trading-python315-probes/quantstats --all-extras --all-groups --python 3.15` | See exit status; no test-count assertion. |
| sync-3.14 | 0 | 0.51s | `uv sync --all-extras --all-groups --python 3.14` | See exit status; no test-count assertion. |
| sync-python315-source-supported | 0 | 1.87s | `uv sync --all-extras --all-groups --python 3.15` | See exit status; no test-count assertion. |
| tests | 1 | 25.68s | `uv run --no-sync python -m pytest` | 10 failed, 215 passed in 24.26s |
| tests-matplotlib-orientation | 0 | 56.05s | `uv run --no-sync python -m pytest` | 225 passed in 54.18s |
| tests-python315-source-supported | 0 | 69.76s | `uv run --no-sync python -m pytest` | 225 passed in 66.75s (0:01:06) |
| versions-final | 0 | 0.14s | `uv run --no-sync python -c import sys,importlib.metadata,json; print(sys.version); print(json.dumps({d.metadata["Name"]:d.version for d in importlib.metadata.distributions()},indent=2,sort_keys=True))` | See exit status; no test-count assertion. |
| wheel-probe-3.14 | 0 | 0.55s | `uv sync --all-extras --all-groups --python 3.14 --no-build --no-install-project` | See exit status; no test-count assertion. |
| wheel-probe-3.15 | 2 | 0.83s | `uv sync --all-extras --all-groups --python 3.15 --no-build --no-install-project` | See exit status; no test-count assertion. |
