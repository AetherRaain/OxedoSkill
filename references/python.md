# Python — polyglot reference (load only in Python projects)

**Dormant by default.** This is for a *future / sibling* Python project, not OxedoScans (which is
React/TS + Cloudflare). Load when working in `.py` files. The three iron laws in `SKILL.md` (TDD,
root-cause debugging, verify-before-claims) still apply.

Distilled from wshobson `python-development` plugin (agents `python-pro`, `fastapi-pro`, `django-pro`
+ 16 skill modules) and jeffallan `python-pro`/`fastapi-expert`/`django-expert`. Target: Python 3.12+,
the 2024/2025 ecosystem.

## Modern tooling (use these, not the legacy stack)

- **uv** — the package manager + venv + Python-version installer. 10–100× faster than pip, written in
  Rust, lockfile-based. `uv init`, `uv add <pkg>`, `uv run <cmd>`, `uv sync`, `uv python install 3.12`.
  Replaces pip/pip-tools/poetry/pyenv. Use it in CI and Docker for fast reproducible builds.
- **ruff** — formatter + linter in one. Replaces black + isort + flake8 + most plugins. `ruff format`,
  `ruff check --fix`. Configure in `pyproject.toml`.
- **mypy** or **pyright** — static type checking. Run in strict mode; treat type errors as build
  failures (the Python analog of `tsc --noEmit`).
- **pyproject.toml** — single source of project config (deps, ruff, mypy, pytest). No `setup.py`.
- **pre-commit** hooks to run ruff + mypy before every commit.

## Type safety (Python's version of TS discipline)

- Annotate all function params, returns, and module-level vars. Types are enforced documentation.
- **Generics** (`class Repo[T]:` in 3.12 syntax) to preserve type info across uses.
- **Protocols** (`typing.Protocol`) for structural interfaces — duck typing *with* static checking,
  no inheritance needed. Prefer over ABCs for "anything shaped like this".
- **Type narrowing**: `isinstance`/`is None` guards, `assert`, `typing.TypeGuard` for custom narrows.
- **pydantic v2** for runtime data validation at boundaries (API input, config). Discriminated unions
  via `Field(discriminator=...)`. Prefer over hand-rolled validation.

## Idioms & patterns

- **Dataclasses / pydantic models** over dict-slinging. `@dataclass(slots=True, frozen=True)` for
  value objects.
- **Context managers** (`with`, `contextlib.contextmanager`) for every resource (files, sockets, DB
  sessions, locks) — RAII for Python. Never leak handles.
- **Structural pattern matching** (`match/case`) for variant dispatch.
- **Error handling**: specific exceptions, not bare `except:`. Chain with `raise X from e`. Define a
  small exception hierarchy per domain. Don't swallow errors silently.
- **Generators / itertools** for memory-efficient streaming over large data; avoid building giant lists.

## Async & performance

- `async`/`await` with asyncio (or trio) for I/O-bound work; `aiohttp`/`httpx` for async HTTP.
- `concurrent.futures` / `multiprocessing` for CPU-bound work (GIL-bound threads won't parallelize CPU).
- Profile before optimizing: `cProfile`, `py-spy`, `memory_profiler`. Cache hot pure calls with
  `functools.lru_cache`.

## Web frameworks

- **FastAPI** — async APIs with pydantic models, automatic OpenAPI, dependency injection. Type-first.
- **Django** — batteries-included; use the ORM well (select_related/prefetch_related to kill N+1),
  django-rest-framework for APIs, migrations always.

## Testing (apply the TDD iron law)

- **pytest** + fixtures + parametrize. `pytest-cov` for coverage, `pytest-benchmark` for perf.
- **Hypothesis** for property-based testing of pure logic and parsers.
- Test behavior, not implementation. Use factories/`tmp_path`/test DBs over heavy mocking.
- Verify gate: run `pytest`, `ruff check`, and `mypy` and read the output before claiming done.
