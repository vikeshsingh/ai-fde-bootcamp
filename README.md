# AI FDE Bootcamp

Code and notes from the AI Forward Deployed Engineer (FDE) Bootcamp, in Python.

Each class lives in its own numbered folder (`class1`, `class2`, ...) with its own
README covering that session's objective and how to run its code.

## Prerequisites

- Python 3.12+
- [uv](https://docs.astral.sh/uv/) for environment and dependency management

## Setup

Clone the repo, then create and sync the environment from the project root:

```bash
uv venv --python 3.12
uv sync
```

Activate it:

```bash
# macOS / Linux
source .venv/bin/activate

# Windows
.venv\Scripts\activate
```

## Running a class's code

No activation needed — `uv run` finds the project's `.venv` automatically:

```bash
uv run class1/hello.py
```

## Repo layout

- [class1/](class1/) — Session 1: environment setup, first script, first notebook.
  See [class1/README.md](class1/README.md) for session notes and
  [class1/uv_cmd.txt](class1/uv_cmd.txt) for the full uv command reference.
- [class2/](class2/) — Session 2: Python fundamentals - variables/data types,
  operators, collections. See [class2/README.md](class2/README.md) for session notes.
- [class3/](class3/) — Session 3: sets, dictionaries, typecasting, input/output,
  copying, string operations. See [class3/README.md](class3/README.md) for session notes.
- [class4/](class4/) — Session 4: control flow, loops, functions, scope.
  See [class4/README.md](class4/README.md) for session notes.
- [class5/](class5/) — Session 5: network I/O, HTTP protocols, the `requests`
  library, memory optimization (generators, iterators, streaming).
  See [class5/README.md](class5/README.md) for session notes.
