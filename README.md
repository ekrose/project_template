# Development Setup Guide (Python Projects)

This project uses **Git**, **pyenv**, **virtual environments**, and **direnv** to ensure:
- reproducible Python versions
- clean dependency management
- no large data files in Git
- minimal editor-specific configuration

Please follow this guide exactly. Most problems come from skipping steps.

---

## 0. What this repo assumes

- You will **not** commit data files to Git
- Python versions are managed with **pyenv**
- Dependencies are installed into a **project-local virtual environment**
- Environment variables are loaded automatically with **direnv**

This setup works on **macOS and Windows** (with small Windows-specific notes below).

---

## 1. Install system prerequisites

### macOS
Install Homebrew if you do not already have it:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"