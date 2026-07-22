# Python environment management

This is a summary of the workflow for managing installed Python versions and isolated, per-project virtual environments using modern tools.

## Sidebar: Why tho?
We have **pip**, **virtualenv**, and a `requirements.txt` file: why would we go to to additional effort of using a package manager? Using pip alone to manage project dependencies is viable, but it has a couple of shortcomings:

- pip doesn't handle your Python executable, only your Python dependencies. So if you need to upgrade Python versions (for example from 3.11 to 3.13), it doesn't assist at all. You could upgrade the system version of Python, but what if you need to maintain access to the old version to support another project? Tools such as **uv** allow you to install and manage a Python executable _specific to your project only_.
- pip doesn't resolve dependencies of dependencies (i.e. the dependency graph). pip will only respect version pinning for dependencies that you explicitly specify. So for example, say I am using pandas and I pin it to version X. If a dependency of pandas (say, numpy) isn't pinned as well, the underlying version of numpy can still change when I reinstall dependencies. A Python environment might stop working despite none of the specified dependencies changing, because underlying dependencies introduced breaking changes. To get around this with pip you would need an additional tool like **pip-tools**, which allows you to pin all dependencies, explicit and nested, to a lock file for true reproducibility. Tools like **Poetry** and **uv** do this out of the box.
- Tool usage. Say there is a Python package you want to use across many environments without installing in the environments themselves (such as a linting tool like **ruff** or a type checker like **ty**). With pip, you need to install another tool like pipx to install something that can be used across environments. Tools like **uv** or **Poetry** can do this out of the box.

# uv

It's 2026, Python tooling continues to improve, and there's a best-in-class tool available to manage Python project environments and dependencies: **[uv](https://github.com/astral-sh/uv)**. It installs and manages Python versions, manages project dependencies, and is _fast_.

Visit the [documentation site](https://docs.astral.sh/uv/getting-started/installation/) for installation and configuration steps.

Installation on Linux consists of:

```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Confirm it works:

```
uv version
```

## Workflow: migrate from pip to uv

1. Use uv to import your existing dependencies and convert `requirements.txt` to `pyproject.toml`:

```
uv pip import requirements.txt
```

2. Synchronise your current dependencies and create a lockfile:

```
uv sync
```

## Workflow: new project set up

1. Initialize a project in the current working directory:

```
uv init --name myprojectname --no-package --app --python 3.13
```

This step will generate several outputs:

- `.python-version`: this file tells uv which Python version to use. Add this file to `.gitignore`.
- `pyproject.toml`: this file contains metadata about your project, and replaces the old `requirement.txt`. Add this file to the Git repo.
- `hello.py`: a sample file to check operation. This can be deleted later.
- `README.md`: information about your project.

`uv` will also helpfully initialise a Git project in the current directory, and generate a `.gitignore` file. 

2. Create your local virtual environment:

```
uv sync
```

This step will generate several outputs:
- `.venv` (directory): this is your local virtualenv directory. Add this to `.gitignore`.
- `uv.lock`: this file is a cross-platform lockfile that contains exact information about your project's dependencies. Add this file to the Git repo.

3. Add additional dependencies to your project (this will also update the lockfile and project environment):

```
uv add requests==2.32.3
```

4. Activate the virtualenv like so:
```
source .venv/bin/activate
```

## Workflow: automatically update project dependencies

Once you have a project with some dependencies installed and some time passes, there's a fair chance that those dependencies (either direct or transitive) will see updates released. Because `uv` calculates the entire dependency graph and preserves this via `uv.lock`, it is straighforward to automatically install compatible package updates like so:

```
uv lock --upgrade
```

This is check PyPI for updated package versions, determine any compatible upgrades, install the upgraded packages locally and update the lockfile.

NOTE: it is best practice to "pin" a project's direct dependency versions at the point of installation (e.g. `uv add django==4.2.18`) in order to ensure consistent project behaviour (especially in multi-developer projects). These dependencies should be updated manually, with appropriate testing carried out.

## Workflow: update project Python version

Let's say that you want to update the Python version from 3.11 to 3.12. Carry out the following process to upgrade the version of Python defined in the project `pyproject.toml` file.

1. Set a new local "default" Python version in the project:

```
uv python pin 3.12
```

2. Update the line in `pyproject.toml`:

```
requires-python = ">=3.12"
```

3. Check the new version:

```
uv run python --version
```

## Workflow: Start an iPython shell session with libraries installed

Say that you just need a quick shell session with a given version of IPython plus `requests` (or whatever else) to check some online resources, not a full-blown virtualenv. The following one-liner creates an ephemeral environment with the nominated libraries installed, runs the given command, and cleans up afterwards:

```
uv run --python 3.12 --with requests,ipython ipython
```

# Other environment management tools

The tools outlined below have previously been recommended, and remain viable options.

## Pyenv

Use **pyenv** to install different specific versions of Python on a host easily, and enable easy switching between them. To install, follow the project [installation instructions](https://github.com/pyenv/pyenv#installation).

Thereafter, use pyenv to install different Python versions on your machine (which can each be used by separate projects):

```
pyenv install 3.7.7
pyenv install 3.12.4
```

Set a default Python version to be used upon entering a project directory:

```
pyenv local 3.12.4
```

Don't add the `.python-version` file to the repository as this is a local-to-you setting; add it to `.gitignore` if necessary. If pyenv is configured properly, the presence of `.python-version` will cause it to activate and use that version of Python automatically when you change into the directory.

Update pyenv periodically like so:

```
pyenv update
````

By default, locally-installed Python versions will be saved at `~/.pyenv/versions`. To remove a version, use `pyenv uninstall <version>`.

## Poetry

pyenv comes with pyenv-virtualenv to manage isolated Python environments. Instead of that, we can use **Poetry** to manage virtual environments and project dependencies (it has better dependency graph management, among other features).

Install Poetry (installs to local user directory, not globally) according to the docs: https://python-poetry.org/docs/#installation

Inside a project directory, initialise the project dependencies (follow the on screen prompts to completion):

```
poetry init
```

Add the `pyproject.toml` and `poetry.lock` files to the project repository, as these are what Poetry uses to track installed depencency versions. Install a new virtual environment for the project:
```

poetry install
```

By default, Poetry installs the new virtual environment locally in the `.venv` directory, so add that directory to your `.gitignore` file. If you ever want to delete and recreate the virtualenv, simply delete this directory and run `poetry install` again.

Add new project dependencies like so (this will update `pyproject.toml` and `poetry.lock`):

```
poetry add requests
poetry add django==5.2.0
poetry add --group=dev ipython
```

See the [dependency specification docs](https://python-poetry.org/docs/dependency-specification/) for more information about specifying package versions, specify dev-only dependencies, etc.

Activate the local virtualenv like so:

```
poetry shell
```

Thereafter you can run Python commands in the shell session as normal. You can also run scripts in the virtual environment without activating it by preceding these with `poetry run`:

```
poetry run python my_script.py
```

# Links

* uv: <https://docs.astral.sh/uv/>
* pyenv: <https://github.com/pyenv/pyenv>
* Poetry: <https://python-poetry.org/>