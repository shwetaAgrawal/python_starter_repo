# Python Basic Template

A minimal Python project template with uv, pytest, mypy, Ruff, nbQA (for notebooks), and handy VS Code tasks.

## Features
- uv for fast dependency and virtualenv management
- pytest with coverage defaults
- mypy preconfigured for a src/ layout
- Ruff for linting and formatting
  - Includes docstring style checks (pydocstyle via Ruff) using the NumPy convention
- nbQA wired to run Ruff on notebooks (.ipynb)
- VS Code tasks for one-shot verify, tests, type-check, and linting
- EditorConfig for consistent whitespace/EOLs

## Tech stack
- Python >= 3.12
- uv (pyproject.toml + dev dependency groups)
- pytest, pytest-cov
- mypy
- ruff
- nbqa

## Layout
```
_template-python-basic/
  ├─ pyproject.toml
  ├─ .gitignore
  ├─ .vscode/
  │   └─ tasks.json
  ├─ src/
  │   └─ example_pkg/
  │       ├─ __init__.py
  │       └─ math_utils.py
  └─ tests/
    ├─ conftest.py
    └─ test_math_utils.py
```

## Getting started
1) Create a project from this template (see "Use it as a GitHub template" below), or copy the folder.
2) Install dependencies:

```bash
uv sync
```

3) Run the basics:

```bash
uv run pytest -q
uv run mypy -p example_pkg
uv run ruff check .
uv run ruff format .
uv run nbqa ruff .
uv run nbqa ruff --fix .
```

4) Optional: clean caches/artifacts quickly

```bash
code --list-extensions > /dev/null # warms VS Code CLI on first run (optional)
```

## Format on save

The workspace settings enable format on save and apply Ruff fixes via code actions. You can still run `ruff format` from tasks if you prefer.

## VS Code tasks
Open the command palette → "Tasks: Run Task" and pick from:

- run:pytest — run tests once
- run:mypy — type check (-p example_pkg)
- run:ruff-format — format Python files
- run:ruff-check-fix — lint and auto-fix Python files
- run:nbqa-ruff-check — lint notebooks via nbQA+Ruff
- run:nbqa-ruff-fix — auto-fix notebooks via nbQA+Ruff
- run:pytest-parallel — run tests in parallel using pytest-xdist (-n auto)
- run:nbmake — execute notebooks as tests (requires .ipynb files)
- run:pytest-htmlcov — build HTML coverage report
- open:coverage — open HTML coverage report in the browser (macOS `open`)
- run:pip-audit — scan for known vulnerabilities
- run:bandit — static security analysis of Python code
- run:pre-commit-all — run all pre-commit hooks on entire repo
- verify — sequential: ruff format → ruff check --fix → nbqa ruff --fix → mypy → pytest
- clean:ruff — remove Ruff caches
- clean:pytest — remove pytest cache and coverage file
- clean:mypy — remove mypy cache
- clean:uv — prune uv cache
- clean:all — run all clean tasks

Tip: bind a key to the "verify" task for a quick pre-commit sweep.

Docstrings
 - This template enforces docstring style checks via Ruff (pydocstyle rules) using the NumPy convention.
 - Tests are excluded from docstring checks; module/package-level docstrings are optional by default.
 - Update `[tool.ruff.lint.pydocstyle]` in `pyproject.toml` to switch conventions (e.g., "google").

## CI: GitHub Actions

This template includes a minimal workflow at `.github/workflows/ci.yml` which runs on pushes and PRs:
- Lint: `uv run ruff check .`
- Type check: `uv run mypy -p example_pkg`
- Tests: `uv run pytest -q`

It installs dependencies via `uv sync --dev` and caches uv and the virtualenv for faster runs.

### Automated dependency updates

Dependabot configuration at `.github/dependabot.yml` will open weekly PRs to update GitHub Actions and pip dependencies.

## Pre-commit hooks

Pre-commit is set up in `.pre-commit-config.yaml` to mirror the local verify flow.

Setup once in your repo:

```bash
uv run pre-commit install
```

Run on all files manually any time:

```bash
uv run pre-commit run --all-files
```

Notes:
- Ruff runs with `--fix` by default in the hook. Use with care on large diffs.
- Black is optional and commented out; uncomment in `.pre-commit-config.yaml` if you prefer it.
- The mypy hook uses `-p example_pkg`. If you rename the package, update this argument accordingly.

## Jupyter notebooks with the venv interpreter

This template is set up so Jupyter uses the project's `.venv` Python:

1) Create the env and install deps:

```bash
uv sync
```

2) Register the venv as a Jupyter kernel (pick one):

- Run the VS Code task: "run:ipykernel-install"

  or

```bash
uv run python -m ipykernel install --user \
  --name "${PWD##*/}-venv" \
  --display-name "Python (${PWD##*/} .venv)"
```

3) In VS Code, open a notebook and select the kernel from the top-right picker:
   "Python (<repo-name> .venv)".

Notes:
- The workspace setting selects `${workspaceFolder}/.venv/bin/python` by default, so terminals and the debugger use the venv.
- If you rename the repo folder, you can re-run the kernel install command to update the display name.

## Rename the package
This template uses `example_pkg`. To rename it automatically, use the built-in task:

- Run VS Code task: "rename:package" and enter your new package name, e.g. `my_project`.

What it does:
- Renames `src/example_pkg` → `src/<your_pkg>`
- Updates imports in `src/` and `tests/`
- Updates `mypy` targets in `.vscode/tasks.json`, `.pre-commit-config.yaml`, and CI workflow
- Updates mentions in `README.md`

You can also run it from a terminal:

```bash
uv run python scripts/rename_package.py my_project
```

## Use it as a GitHub template
1) Push this folder to a GitHub repo, e.g. `python-basic-template`.
2) On GitHub → Settings → General → Template repository → check "Template repository".
3) To create a new project:
  - Web: Click "Use this template" on the repo page.
  - GitHub CLI:

```bash
gh repo create <you>/<new-project> --template <you>/python-basic-template --public
```

Then:

```bash
git clone https://github.com/<you>/<new-project>.git
cd <new-project>
uv sync
uv run pytest -q
```

## Logs
Quick usage
In code:

```
from example_pkg.logging_utils import get_logger

log = get_logger(__name__)
log.info("hello")
```

Pick format and level via env:
macOS/zsh examples:
```bash
LOG_FORMAT=json LOG_LEVEL=DEBUG uv run python -c 'from example_pkg.logging_utils import get_logger; get_logger(__name__).debug("debug msg", extra={"user":"alice"})'
LOG_FORMAT=plain LOG_COLOR=1 uv run python -c 'from example_pkg.logging_utils import get_logger; get_logger().warning("color warning")'
```

## Notes
- The `tests/conftest.py` ensures `src/` is importable when running pytest from repo root.
- nbQA tasks will do nothing if you don’t have any `.ipynb` files yet; they’ll kick in once notebooks exist.
- Add a LICENSE file (MIT/Apache-2.0) if you plan to share publicly.

## License

This template is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.
