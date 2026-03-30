# AGENTS.md

This file provides operating guidance for agentic coding tools in this repo.

## Project Profile
- Name: `luc-cs` (Loyola University Chicago course scheduling utility)
- Language: Python
- Required Python: `~=3.13` (`pyproject.toml`)
- Build backend: Hatchling
- Environment/dependency manager: `uv`
- CLI entrypoint: `luc-cs` -> `cs.main:main`
- App runtime: Streamlit (`cs/app.py`)
- Core libraries: pandas, numpy, plotly, streamlit, pydantic

## Rule Files (Cursor / Copilot)
Repository checks performed:
- `.cursorrules`: not found
- `.cursor/rules/`: not found
- `.github/copilot-instructions.md`: not found
If these files are added later, treat them as higher-priority instructions.

## Setup Commands
Primary setup:
```bash
uv sync
```
Optional bootstrap target:
```bash
make create-dev
```
`make create-dev` currently runs:
- `pre-commit install`
- `pre-commit autoupdate`
- `uv sync`
- `uv build`

## Build Commands
Preferred build:
```bash
make build
```
Equivalent direct build flow:
```bash
uv build
uv pip install dist/*.tar.gz
```
Run the app:
```bash
make run
# or
.venv/bin/streamlit run cs/app.py
```

## Lint / Format / Security Commands
Run full configured checks:
```bash
pre-commit run --all-files
```
Configured hooks include:
- `ruff-format`
- `ruff-check`
- `isort`
- `bandit`
- `pyroma`
- `pre-commit-hooks` core file checks
Useful direct commands:
```bash
uv run ruff format .
uv run ruff check .
uv run isort .
uv run bandit -r cs
```

## Test Commands (Including Single Tests)
Current repository state: no `tests/` directory exists.
When tests are introduced, use `pytest` via `uv run`.
Run full suite:
```bash
uv run pytest
```
Run one test file:
```bash
uv run pytest tests/test_example.py
```
Run one test function:
```bash
uv run pytest tests/test_example.py::test_case_name
```
Run one test class:
```bash
uv run pytest tests/test_example.py::TestClassName
```
Run by keyword:
```bash
uv run pytest -k "keyword"
```
Stop at first failure:
```bash
uv run pytest -x
```
If `pytest` is missing, add it to dev dependencies before relying on tests.

## Code Style Guidelines

### Imports
- Prefer absolute imports from `cs` modules.
- Group imports by stdlib, third-party, then local package.
- Let `isort` and Ruff formatting control final import layout.

### Formatting
- Ruff formatter is authoritative.
- Ruff line length is `88` (`ruff.toml`).
- `isort` uses Black profile with import line length `79` (`.isort.cfg`).
- Use 4-space indentation.
- Prefer double quotes.

### Types
- Add type hints for new public functions and methods.
- Annotate return types explicitly (`-> None` when appropriate).
- Prefer modern built-in generics (`list[str]`, `dict[str, int]`).
- Keep typing style consistent with the surrounding module.

### Naming
- Functions, variables, and modules: `snake_case`.
- Classes: `PascalCase`.
- Constants: `UPPER_SNAKE_CASE`.
- Avoid renaming existing public symbols unless required.

### Docstrings
- Public classes and functions should have docstrings.
- Existing code often uses reST-style tags (`:param`, `:rtype:`).
- Keep docstrings concise and behavior-centric.

### Error Handling
- Do not use bare `except:`.
- Catch specific exceptions.
- At UI boundaries, provide user-visible feedback (e.g., `streamlit.error`).
- Avoid silently swallowing exceptions unless justified in code comments.

### Data and SQL
- Normalize spreadsheet/DataFrame columns early (uppercase is common here).
- Keep DataFrame transformations explicit and readable.
- Avoid unsafe dynamic SQL from untrusted input.
- If `# nosec` is necessary, keep scope narrow and include rationale.

### Security and Quality
- Run `bandit` and address findings or document accepted risk.
- Never commit secrets, credentials, or private keys.
- Do not bypass pre-commit hooks in normal workflows.

## Recommended Agent Workflow
1. Read relevant files and match local patterns.
2. Keep edits minimal and scoped to the requested task.
3. Run `pre-commit run --all-files`.
4. Run tests if present; otherwise explicitly note they are absent.
5. Report what changed, what was validated, and any residual risk.

## Current Repo Notes
- `README.md` contains legacy references to `PRIME`.
- Automated tests are currently absent.
- Existing style is mixed in some modules; prefer incremental consistency.

Update this file when commands, tooling, or repository rule files change.
