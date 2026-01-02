# Development Setup Guide (Python Projects)

This project uses **Git**, **pyenv**, **virtual environments**, and **direnv** to ensure:
- reproducible Python versions
- clean dependency management
- no large data files in Git
- minimal editor/user-specific configuration



---

## 0. What this repo assumes

- You will **not** commit data files to Git
- Python versions are managed with **pyenv**
- Dependencies are installed into a **project-local virtual environment**
- Environment variables are loaded automatically with **direnv**

This setup works on **macOS and Windows** (with small Windows-specific notes below).



---

## Section 1 — Install prerequisites

### macOS

Install Homebrew (if needed):

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

Install required tools:

`brew install git pyenv direnv`

Verify:

```
git --version  
pyenv --version  
direnv --version 
```

---

### Windows (recommended: WSL)

Install Windows Subsystem for Linux (Ubuntu):  
https://learn.microsoft.com/en-us/windows/wsl/install

Inside the WSL terminal, install tools:

```
sudo apt update  
sudo apt install -y git curl build-essential direnv  
```

Install pyenv:

`curl https://pyenv.run | bash`

From this point forward, all commands must be run inside WSL, not PowerShell.

---

## Section 2 — Configure pyenv (one time)

### macOS (zsh)

Edit ~/.zshrc and add:

`export PYENV_ROOT="$HOME/.pyenv"  
export PATH="$PYENV_ROOT/bin:$PATH"  
eval "$(pyenv init -)"`

Reload:

`exec $SHELL`

Verify:

`pyenv versions`

---

### Windows / WSL (bash)

Edit ~/.bashrc and add:

`export PYENV_ROOT="$HOME/.pyenv"  
export PATH="$PYENV_ROOT/bin:$PATH"  
eval "$(pyenv init -)"`

Reload:

`source ~/.bashrc`

Verify:

`pyenv versions`

---

## Section 3 — Install the project Python version

From the repository root:

`pyenv install 3.14.2  
pyenv local 3.14.2`

Verify:

`python --version  `

Expected: Python 3.14.2

---

## Section 4 — Create and activate the virtual environment

From the repo root:

`python -m venv .venv`

Activate (macOS / WSL):

`source .venv/bin/activate`

Verify:

`which python`

The path must include .venv.

---

## Section 5 — Install Python dependencies

If requirements.txt exists:

`python -m pip install --upgrade pip  
pip install -r requirements.txt ` 

---

## Section 6 — Make pip safe (IMPORTANT)

Run once per machine:

`python -m pip config set global.require-virtualenv true`

This will ensure pip will not install any packages outside of the 

---

## Section 7 — Global Git hygiene (one time)

Create `~/.gitignore_global` with:

````__pycache__/  
# ---- Python ----
__pycache__/
*.py[cod]
*.pyo
*.pyd
*.so
*.egg-info/
.eggs/

# Virtual environments
.venv/
venv/
ENV/
env/

# Python tooling
.pytest_cache/
.mypy_cache/
.pyre/
.pytype/
.coverage
htmlcov/

# Jupyter
.ipynb_checkpoints/

# ---- macOS ----
.DS_Store
.AppleDouble
.LSOverride

# ---- Editors ----
.vscode/
.idea/
*.swp
*.swo
*~
````

Register it:

`git config --global core.excludesfile ~/.gitignore_global`

Verify:

`git config --global --get core.excludesfile`

---

## Section 8 — Data workflow (CRITICAL)

Never commit data files to Git.

Large data files live outside the repo (e.g. Dropbox).

Data paths are provided via the environment variable:

`PROJECT_DATA_DIR`

Example in Python:

```
from pathlib import Path  
import os  

DATA_DIR = Path(os.environ["PROJECT_DATA_DIR"])
```

---

## Section 9 — direnv setup

Enable direnv in your shell.

macOS (.zshrc):

`eval "$(direnv hook zsh)"`

WSL (.bashrc):

`eval "$(direnv hook bash)"`

Reload your shell.

Create your local .envrc:

`cp .envrc.example .envrc`

Edit .envrc and set:

`export PROJECT_DATA_DIR=/path/to/your/data`

Allow it:

`direnv allow`

Verify:

`echo $PROJECT_DATA_DIR`



---

## Section 10 — Daily checklist

1. cd into repo
2. direnv allow (if first time)
3. source .venv/bin/activate
4. Run code via Interactive Window in editor of choice (e.g., Cursor)
5. Never commit data

---