# Python Poetry Test Action

Test Poetry-based projects using ruff, mypy, and pytest.

1. Install Python
2. Installs Poetry
3. Installs dependencies
4. Runs `ruff check` on source directories
5. Runs `ruff format --check` on source directories
6. Runs `mypy` on source directories
7. Runs `pytest`

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version` | Python version to use | No | `3.14` |
| `poetry-version` | Poetry version to install | No | `2.2.1` |
| `src-dirs` | Source directories for linting (space-separated) | No | `.` |

## Usage

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Test with Poetry
    uses: CVector-Energy/python-test@main
```

### With custom Python version

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Test with Poetry
    uses: CVector-Energy/python-test@main
    with:
      python-version: "3.12"
```

### Matrix testing

```yaml
jobs:
  test:
    runs-on: ubuntu-24.04
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12", "3.14"]

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Test with Poetry
        uses: your-org/python-test@main
        with:
          python-version: ${{ matrix.python-version }}
```
