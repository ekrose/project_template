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

pyenv allows us to make sure we are consistently using the same version of python. We will install it, and then configure it so that all the code we execute from the project directory uses the specified version of python (3.14.2 in this example).

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

We now specify the python version we want to use in this project.

From the repository root:

`pyenv install 3.14.2  
pyenv local 3.14.2`

Verify:

`python --version  `

Expected: Python 3.14.2

---

## Section 4 — Create and activate the virtual environment

Finally, we use a venv to manage packages / dependences in thie project.

From the repo root:

`python -m venv .venv`

Activate (macOS / WSL):

`source .venv/bin/activate`

Verify:

`which python`

The path must include .venv.

---

## Section 5 — Make pip venv safe

You only want use pip to install packages within the project venv. One way to ensure this happens is to require that pip only work inside of a venv.

Run once per machine:

`python -m pip config set global.require-virtualenv true`

---

## Section 6 — Install Python dependencies

If requirements.txt exists:

`python -m pip install --upgrade pip  
pip install -r requirements.txt ` 

Otherwise, you can install some packages and save the requirements (be sure to update as you add new packages):

```
pip install numpy scipy pandas statsmodels matplotlib seaborn
pip freeze > requirements.txt
```

---

## Section 7 — Global Git hygiene (one time)

There are lots of things that should not be tracked in git. We will make a local .gitignore, but it's useful to have a global one that applies across all our work.

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

## Section 8 — Data workflow 

Never commit data files to Git. Large data files live outside the repo, either on your hard drive, on a server, or in Dropbox.

To make sure the code can be used by everyone, data paths are provided via the environment variable:

`PROJECT_DATA_DIR`

**Important:** File paths should never be hard coded (only relative paths).

Example in Python:

```
from pathlib import Path  
import os  

DATA_DIR = Path(os.environ["PROJECT_DATA_DIR"])
```

---

## Section 9 — direnv setup

Because paths are defined by an environment variable, it's useful to have these variables available whenever we work on the project. We use `direnv` to accomplish this.

Enable `direnv` in your shell.

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

## Section 11 — Forking this repo

You may want to fork this repo for each new project. When you do so, ensure that you:

1. Delete `.python-version` and specify the desired python version via `pyenv local 3.X.X`.
2. Create your environment variable via `cp .envrc.example .envrc` and adjust paths.
3. Install the virtual environment `python -m venv .venv` and install requirements via pip.
4. Create and just local `.gitignore` for project-specific files we don't want to commit.