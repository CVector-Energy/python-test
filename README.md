# CVector Python Test Action

Test Python projects using ruff, mypy, and pytest. Supports both UV and Poetry — auto-detected from your lock file.

## What This Action Does

1. Installs Python
2. Detects package manager (UV or Poetry) from lock file
3. Installs the package manager and syncs dependencies (with caching)
4. Runs `ruff format` on source directories (auto-fixes in place)
5. Runs `ruff check --fix` on source directories (auto-fixes in place)
6. If checks fail and `repair-token` is set, commits and pushes fixes
7. Runs `mypy` on source directories
8. Runs `pytest`

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version` | Python version to use | No | `3.14` |
| `package-manager` | `auto`, `uv`, or `poetry` | No | `auto` |
| `uv-version` | UV version to install | No | `0.11.2` |
| `poetry-version` | Poetry version to install | No | `2.3.3` |
| `src-dirs` | Source directories for linting (space-separated) | No | `.` |
| `working-directory` | Directory containing pyproject.toml | No | `.` |
| `run-pytest` | Whether to run pytest | No | `true` |
| `repair-token` | Token for git push. Enables auto-repair on failure. | No | `""` |

## Usage

### Basic Usage (auto-detects UV or Poetry)

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Test
    uses: CVector-Energy/python-test@main
```

### With Custom Python Version

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Test
    uses: CVector-Energy/python-test@main
    with:
      python-version: "3.12"
```

### Matrix Testing

```yaml
jobs:
  test:
    runs-on: ubuntu-24.04
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12", "3.13", "3.14"]

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Test
        uses: CVector-Energy/python-test@main
        with:
          python-version: ${{ matrix.python-version }}
```

### With Auto-Repair on Push

When checks fail, the repair step runs `ruff check --fix` and `ruff format`, then commits the fixes back to the branch via the GitHub REST API. Use a GitHub App token so the resulting commit triggers a new workflow run (a `GITHUB_TOKEN`-authored commit will not).

The App must be installed on the repository with **Contents: Read and write**.

```yaml
jobs:
  test:
    runs-on: ubuntu-24.04
    steps:
      - name: Create GitHub App Token
        id: app
        if: github.event_name == 'push'
        uses: actions/create-github-app-token@v2
        with:
          app-id: ${{ vars.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}

      - name: Checkout
        uses: actions/checkout@v6

      - name: Test
        uses: CVector-Energy/python-test@main
        with:
          repair-token: ${{ steps.app.outputs.token }}
```

## Requirements

Your project must have:
- `pyproject.toml` with `[project]` section and `requires-python`
- `uv.lock` (for UV) or `poetry.lock` (for Poetry)
