# Loyola University Chicago Course Scheduling Utility (`luc-cs`)

`luc-cs` is a Streamlit-based utility for exploring and analyzing Loyola
University Chicago course scheduling data exported from Locus as Excel files.

The app loads schedule data into SQLite, normalizes key fields, and exposes
interactive analytics to help department leadership and faculty review
enrollment, assignments, scheduling patterns, and potential issues.

## Table of Contents

- [What This Project Is](#what-this-project-is)
- [Requirements](#requirements)
- [Create the Virtual Environment](#create-the-virtual-environment)
- [Build the Project](#build-the-project)
- [Run the Project](#run-the-project)
- [Use the Application](#use-the-application)
- [Input Data Schema](#input-data-schema)
- [Troubleshooting](#troubleshooting)
- [Architecture Overview](#architecture-overview)
- [Development Checks](#development-checks)
- [Testing](#testing)
- [License](#license)

## What This Project Is

This project provides a local analytics app for course scheduling workflows.
You can select an existing `.xlsx` file in the project directory or upload one
in the UI, then run analytics such as:

- Course Schedule
- Online Only Courses
- Schedule Density
- Course Enrollment Health
- Instructor Assignments
- Courses with No Enrollments
- Number of Assignments Per Faculty Member
- Course by Number
- Teaching Distribution by Weighted Enrollment
- Enrollments by Course Level
- In Trouble Courses
- Filter Course Schedule
- School Credit Hours

## Requirements

- Python `~=3.13`
- [`uv`](https://docs.astral.sh/uv/) for environment and dependency management
- A course schedule Excel export (`.xlsx`) from Locus

## Create the Virtual Environment

The recommended setup command is:

```bash
uv sync
```

This creates/updates `.venv` and installs all project dependencies.

Optional activation commands:

```bash
# Linux / macOS
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

## Build the Project

Preferred build path:

```bash
make build
```

Equivalent direct commands:

```bash
uv build
uv pip install dist/*.tar.gz
```

## Run the Project

Start the Streamlit app:

```bash
make run
```

Equivalent direct command:

```bash
.venv/bin/streamlit run cs/app.py
```

You can also run the package CLI entrypoint:

```bash
uv run luc-cs
```

Note: `luc-cs` prints runtime instructions; the Streamlit command starts the UI.

## Use the Application

1. Launch the app with one of the commands above.
2. Choose an existing `.xlsx` file from the project folder, or upload one.
3. Click any analytic button to generate charts/tables.
4. Use the zero-enrollment filter checkbox when relevant to your review.

## Input Data Schema

The app expects a Locus-exported Excel workbook (`.xlsx`) with course schedule
columns. During ingestion, columns are normalized to uppercase and additional
derived fields are computed.

Required input columns used by the app:

- `SUBJECT`
- `CATALOG NUMBER`
- `SECTION`
- `CLASS TITLE`
- `INSTRUCTOR`
- `FACILITY`
- `MEETING PATTERN`
- `CLASS START TIME` (time-like values)
- `CLASS END TIME` (time-like values)
- `ENROLLMENT TOTAL`

Key normalized/derived columns created in the pipeline:

- `ENROLL TOTAL` (from `ENROLLMENT TOTAL`)
- `FQ CATALOG NUMBER` (`SUBJECT-CATALOG NUMBER`)
- `FQ CLASS SECTION` (`CATALOG NUMBER-SECTION`)
- `TRAD MEETING PATTERN` (normalized meeting pattern)
- `UNIT CLASS DURATION` (minutes)
- `COMBINED ID`
- `INSTRUCTIONAL TIME`
- `WEIGHTED ENROLL TOTAL`
- `WEIGHTED SCH TOTAL`

Data handling notes:

- Missing `INSTRUCTOR` values are filled with `Turing,Alan`.
- Missing `FACILITY` values are filled with `Doyole Hall`.
- Duplicate rows are dropped by `FQ CLASS SECTION`.

## Troubleshooting

Common issues and fixes:

- **`uv: command not found`**
  - Install `uv` first, then rerun `uv sync`.
- **Python version mismatch**
  - The project requires Python `~=3.13`; ensure your interpreter matches.
- **No `.xlsx` file appears in the selector**
  - Place an Excel export in the repository root or upload through the app.
- **Streamlit fails to start**
  - Re-run `uv sync` and then use `make run` or
    `.venv/bin/streamlit run cs/app.py`.
- **Build fails on version/tag step in `make build`**
  - Ensure Git tags are available locally; otherwise use direct build commands:
    `uv build` and `uv pip install dist/*.tar.gz`.
- **Pre-commit checks fail**
  - Run `pre-commit run --all-files`, apply suggested fixes, and re-run.

## Architecture Overview

High-level module layout:

- `cs/app.py`
  - Streamlit entrypoint and UI orchestration.
  - Handles file selection/upload and analytic button routing.
- `cs/excel2db.py`
  - Excel ingestion and normalization.
  - Loads transformed schedule data into SQLite.
- `cs/analytics/`
  - Individual analytics modules (tables/plots) for scheduling and enrollment
    analysis.
- `cs/utils/__init__.py`
  - Shared session-state and utility helpers.
- `cs/main.py`
  - CLI helper entrypoint that prints run instructions.

Runtime flow:

1. User launches Streamlit app.
2. App ingests selected/uploaded Excel data into SQLite.
3. Analytics modules query/transform data and render DataFrames/figures.
4. Results are displayed in Streamlit with shared session state.

## Development Checks

Run all configured checks:

```bash
pre-commit run --all-files
```

Useful direct tooling commands:

```bash
uv run ruff format .
uv run ruff check .
uv run isort .
uv run bandit -r cs
```

## Testing

There is currently no `tests/` directory in this repository.

When tests are added, run:

```bash
uv run pytest
```

## License

This project is licensed under the GNU Affero General Public License v3.
