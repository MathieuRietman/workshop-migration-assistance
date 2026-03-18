---
name:  python-environment
description: Always use this skill when the user asks to manage Python environments, install dependencies, or create virtual environments.
---

# Python Environment and UV Skill



## Purpose
Manage Python package installation and virtual environments efficiently using `uv`. If `uv` is not installed, install it first.

## Instructions

### 1. Check for `uv` Installation
Run `uv --version` to check if `uv` is installed.
- **If installed**: Proceed to environment management.
- **If not installed**: Install `uv` using the appropriate method for the OS:
  - **Windows**: `irm https://astral.sh/uv/install.ps1 | iex` (PowerShell) or use `pip install uv` if Python is already available.
  - **macOS/Linux**: `curl -LsSf https://astral.sh/uv/install.sh | sh` or use `pip install uv`.

### 2. Create Virtual Environment
Use `uv` to create a virtual environment instead of `python -m venv`.
- **Command**: `uv venv`
  - This creates a `.venv` directory in the current workspace by default.
  - To specify a Python version: `uv venv --python 3.12` (or desired version).

### 3. Activate Environment
- **Windows (PowerShell)**: `.venv\Scripts\activate`
- **macOS/Linux**: `source .venv/bin/activate`

### 4. Install Dependencies
Use `uv pip` for fast package installation.
- **Install from requirements**: `uv pip install -r requirements.txt`
- **Install single package**: `uv pip install <package_name>`
- **Install in specific venv**: `uv pip install -r requirements.txt --python .venv` (if not activated)

### 5. Add/Remove Dependencies
- **Add**: `uv add <package>` (updates `pyproject.toml` if present)
- **Remove**: `uv remove <package>`

## Best Practices
- Prefer `uv` over `pip` and `venv` for speed and reliability.
- Always ensure `.venv` is added to `.gitignore`.
- Use `uv sync` to sync the environment with `uv.lock` if using `uv` project management features.
