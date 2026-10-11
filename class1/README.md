# Class 1: Environment Setup

## Objective

Set up a Python environment with `uv`, run a first script, and run a first notebook.

## Files

- `hello.py` — minimal script to confirm the environment works.
- `first.ipynb` — first Jupyter notebook exercise.
- `uv_cmd.txt` — reference for creating, activating, and running code with `uv` environments.
- `screenshot/` — reference screenshots for notebook exercises.

## Run it

From the project root:

```bash
uv run class1/hello.py
```

For the notebook, open `class1/first.ipynb` in Jupyter or VS Code with the project's `.venv` selected as the kernel.

## Class notes

**Working style for this bootcamp:** prefer debugging/implementation questions
("I'm getting this error", "how do I do X") with an LLM first, and only bring
true conceptual doubts to the instructor. No single LLM is "best" — try
several and compare.

**Tooling stack covered:**
- **Terminal / CLI** — the command-line interface built into the OS (Bash/Zsh
  on Mac, PowerShell/Command Prompt on Windows). Servers typically have no UI
  at all — just an OS (usually Linux) reachable only via terminal — so CLI
  fluency is required, not optional.
- **Editor vs. hybrid IDE vs. full IDE:**
  - *Editor* (e.g. Notepad, `nano`) — create/edit files only, no dev tooling.
  - *Hybrid IDE* (VS Code) — editor + integrated terminal + debugger +
    extensions (AI agents, Docker, cloud connectors, etc.), lightweight.
    This is the tool used in this course.
  - *Full IDE* (PyCharm) — language-specific, feature-rich, but heavier on
    RAM/CPU/storage than needed for most of this course's work.
- **Python install options:**
  1. `python.org` installer — simplest, but only one Python version at a
     time per machine.
  2. Anaconda/Miniconda (`conda`) — full data-science toolkit, manages
     multiple Python versions, but heavy.
  3. **`uv`** — lightweight, manages multiple Python versions via named
     virtual environments. **Recommended path for this course.**
- **Virtual environments:** a named environment pins a specific Python
  version to a project (e.g. env `A` → Python 3.4 for a legacy project, env
  `B` → Python 3.12 for a new one). `uv` lets you switch between them
  instantly instead of installing/uninstalling Python per project.
