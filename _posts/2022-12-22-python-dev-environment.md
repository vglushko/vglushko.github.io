---
layout: post
title: "Development Environment for Python Projects"
date: 2022-12-22 09:00:00 +0300
category: development
tags: 
  - python3
  - pyenv
  - poetry
  - flake8
  - black
  - mypy
  - make
  
---
Starting a new team project takes a lot of efforts. Part of them is the development and implementation of a strategy for configuring the local development environment that every teammate uses for their everyday coding activities. The ultimate goal here is to build the environment that assists the developers to keep high the quality of the code they develop as a part of one team. Traditionally, this goal is achieved by following a team coding guidelines and development standards.<!--more--> 

Most often these guidelines have the form of dos and don'ts. Usually, they contain code formatting rules, naming conventions, programming recommendations, etc. In Python world there is the [PEP 8 - Style Guide for Python Code](https://peps.python.org/pep-0008/){:target="_blank"} that one can use as a base to build their own guidelines upon it. However, having the coding guidelines in place is just a part of the solution. Another part is to force their use constantly and consistently across the project. This is where the tools come to the rescue. Generally, the tools have to cover the core aspects of the software development that should be similarly followed across the team. This includes dependency management and packaging, code formatting and style checking (or linting), static type checking (if you use annotations and you should!). 

Let's consider one particular setup that I found the most productive for me so far. I am going to use Ubuntu as my development operating system and bash as the shell. The version of Python I use below is 3.10, but this setup can be used with other Python versions which are compatible with the tools. I am going to cover the toolset for the following aspects of software development:

* Managing different Python versions on the same machine with `pyenv`{:.bash} Python manager
* Configuring the virtual environment for the Python project
* Standardizing dependency management process with `Poetry`{:.bash}
* Defining consistent code formatting rules by means of `Black`{:.bash}
* Organizing code style checking with `flake8`{:.bash}
* Executing static type checking with `mypy`{:.bash}
* Invoking the configured tools with `Make`{:.bash}

# Setting Up Python Manager
According to the [Status of Python Versions](https://devguide.python.org/versions/){:target="_blank"} page, a new Python version is released every year. There are 5 major Python versions actively supported at the time of this writing (Python 3.7 - Python 3.11). There is a big chance that you may work with different version of Python on the same machine at the same time. A Python version manager can assist you in installing, using and managing separate Python versions on your operating system. A [`pyenv`{:.bash}](https://github.com/pyenv/pyenv){:target="_blank"} tool is the Python version manager of the choice. On Ubuntu/bash it can be setup as follows (check `pyenv`{:.bash} [documentation](https://github.com/pyenv/pyenv/blob/master/README.md){:target="_blank"} for details and other operating systems):

```bash
# Install the latest pyenv version
curl https://pyenv.run | bash
# Restart the shell
exec $SHELL
# Update ~/.bashrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
# Update ~/.profile
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.profile
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.profile
echo 'eval "$(pyenv init -)"' >> ~/.profile
# Restart the shell
exec $SHELL
```

Show the pyenv version to find out if it works correctly:
```bash
pyenv --version
```

Now, install Python version through the pyenv:
```bash
# Let's install Python 3.10.8
pyenv install 3.10.8
```

Switch to the installed Python v3.10.8 in the current directory and for all subdirectories:
```bash
pyenv local 3.10.8
```
It creates `.python-version`{:.bash} file with the specified Python version in the current directory which sticks your local environment to Python 3.10.8 for `pyenv`{:.bash}. You can check the currently active version of Python by running again:
```bash
python --version
```

Check the `pyenv`{:.bash} [documentation](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-latest){:target="_blank"} for other commands.

# Setting Up a Virtual Environment
A virtual environment is a separate hidden directory inside a Python project. It is used to install all packages needed for the project locally. The idea is to isolate project packages in the project directory to avoid mess with any Python packages installed globally. 

Since version 3.3, Python includes the [`venv`{:.bash}](https://docs.python.org/3/library/venv.html){:target="_blank"} module that can be used to create a virtual environment.
Execute the following command to create the virtual environment in the project current directory:

```bash
python -m venv .venv
```
It creates the `.venv`{:.bash} sub-directory in the current directory. The `.venv`{:.bash} directory contains a copy of Python, pip and packaging packages.

The newly created environment should be activated before it can be used:
```bash
source .venv/bin/activate
```
You should see the (.venv) prefix at the beginning of the command line prompt now as a sign of the active virtual environment. Make sure that you activate your virtual environment before running any tools discussed below.

# Poetry for Dependency Management
[`Poetry`{:.bash}](https://python-poetry.org/docs/){:target="_blank"} is a tool for dependency management and packaging. It is alternative to [`pip`{:.bash}](https://pip.pypa.io/en/stable/){:target="_blank"} package installer for Python. The key features of Poetry as the dependency management tool are declaration and management of project libraries and lockfile for repeatable installs. Both features help to reproduce the exact version of the project dependencies in the project environments of the teammates. They protect the project from the drift of version dependencies.

`Poetry`{:.bash} can be installed into the project virtual environment as follows:
```bash
# Update 'pip' package and install the 'setuptools' and 'packaging' packages
.venv/bin/pip install -U pip setuptools packaging
# Install poetry
.venv/bin/pip install poetry
```

Then `Poetry`{:.bash} can either generate a new project structure or initialize itself in the existing project folder.

To generate a new project run:
```bash
poetry new <project-package-name>
```
To initialize `Poetry`{:.bash} for an existing project run in the project root folder:
```bash
poetry init
```
In both cases `Poetry`{:.bash} generates a [`pyproject.toml`{:.bash}](https://toml.io/en/){:target="_blank"} file that contains all project dependencies.

`Poetry`{:.bash} organizes all project dependencies into [dependency groups](https://python-poetry.org/docs/managing-dependencies/#dependency-groups){:target="_blank"}. All dependency groups are defined in the pyproject.toml. `Poetry`{:.bash} defines 2 specific dependency groups: `main`{:.ini} and `dev`{:.ini}. The `main`{:.ini} group should contain all dependencies need for the project at runtime. The `dev`{:.ini} group should contain dependencies of the development time (ex. `pytest`{:.bash}, `black`{:.bash}, `flake8`{:.bash}, `mypy`{:.bash}, etc.). `Poetry`{:.bash} also supports user-defined dependency groups. Some groups can be set as optional.

To add and install a dependency named `dep-name`{:.bash} to the main dependency group run
```bash
poetry add dep-name
```
As the result, `Poetry`{:.bash} adds the `dep-name`{:.bash} dependency of a selected version to the `[tool.poetry.dependencies]`{:.ini} main group in the `pyproject.toml`{:.bash} file. It also updates the `poetry.lock`{:.bash} file with the exact versions of the dependency package being installed and all other dependency packages it requires, if any:
```ini
[tool.poetry.dependencies]
python = "^3.10"
dep-name = "^x.y.z"
```

To install a dependency (ex. `pytest`{:.bash}) to any other dependency group, including the development group, run
```bash
poetry add pytest -G dev
```
This modifies the development dependency group in the `pyproject.toml`{:.bash} file, updates the `poetry.lock`{:.bash} file and installs the `pytest`{:.bash} dependency to the active virtual environment:
```ini
[tool.poetry.group.dev.dependencies]
pytest = "^7.2.0"
```

To remove a dependency from a dependency group run:
```bash
poetry remove pytest -G dev
```

To install all dependencies from all non-optional groups run:
```bash
poetry install
```
It installs all immediate dependencies from the `pyproject.toml`{:.bash} file if `poetry.lock`{:.bash} is missing, or reproduces exact dependency versions from `poetry.lock`{:.bash} (which you must keep in your team version control repository to share among the teammates).

All above just scratches `Poetry`{:.bash} features. I suggest to check the Poetry [documentation](https://python-poetry.org/docs/){:target="_blank"} for information about dependency groups, virtual environments, package building and publishing, private repositories and more.

# Black for Code Formatting
[`Black`{:.bash}](https://black.readthedocs.io/en/stable/index.html){:target="_blank"} is a [PEP 8](https://peps.python.org/pep-0008/){:target="_blank"} compliant code formatter. Coding style used by `Black`{:.bash} is a strict subset of PEP 8. `Black`{:.bash} is configured with good default settings that you can start with. It also smoothly integrates with other tools, like `flake8`{:.bash} and `isort`{:.bash} (there is a great chance that you use `isort`{:.bash} to sort your imports in the file).

`Black`{:.bash} should be installed as a development time tool into the development dependency group:

```bash
poetry add black -G dev
```

`Black`{:.bash} can be run to scan the files in the current directory and all its subdirectories by executing:

```bash
black .
```
You can also specify the path to a single file or group of files instead of the current directory as the starting point.

`Black`{:.bash} behavior can be tweaked in the `pyproject.toml`{:.bash} file. By default, `Black`{:.bash} searches for this file starting from the common base directory passed on the command line and then up by the directory tree starting from the parent directory. In the TOML file, `Black`{:.bash} looks for the `[tool.black]`{:.ini} section for its settings. 

Most of the time `Black`{:.bash}'s default settings work OK and you don't need to tweak them. The only setting you could consider to change is the line length. Personally, I find comfortable to set text lines to 119 characters long, though the recommended settings are 88 characters per line:

```ini
[tool.black]
line-length = 119
```

By default, `Black`{:.bash} ignores files that you configure in your `.gitignore`{:.bash} file. You can also add exclusions directly into the `pyproject.toml`{:.bash} file using the set of exclude* configuration options.

`Black`{:.bash} supports skipping of the formatting for lines that ends with `# fmt: skip`{:.python} comments. It can also skip whole code blocks that starts with `# fmt: off`{:.python} and ends with `# fmt: on`{:.python}. You will rarely need that but it is good to know that you still can turn formatting off for some tricky cases.

`Black`{:.bash} runs smoothly with other Python tools but still requires some tweaks that fixes the conflicting behavior. Two tools of interest for this article are `isort`{:.bash} and `flake8`{:.bash}. 

The `isort`{:.bash} tool is used to sort and format imports in your Python code. Since version 5.0.0 `isort`{:.bash} supports profiles then `Black`{:.bash} compatibility can be turned on by configuring the following section in the `pyproject.toml`{:.bash} file:

```ini
[tool.isort]
profile = "black"
```
Check the [isort integration](https://black.readthedocs.io/en/stable/guides/using_black_with_other_tools.html#isort){:target="_blank"} in the `Black`{:.bash} documentation for details.

The `flake8`{:.bash} integration with `Black`{:.bash} is considered in the `flake8`{:.bash} dedicated [section](#flake8-for-style-checking) below.

# Flake8 for Style Checking
[`Flake8`{:.bash}](https://github.com/PyCQA/flake8){:target="_blank"} is a linting tool that checks code for best practices of Python coding. It is a wrapper around [pyflakes](https://github.com/PyCQA/pyflakes){:target="_blank"} which is the static code analysis tool, [pycodestyle](https://pep8.readthedocs.io/en/latest/){:target="_blank"} which is the code style checking tool and [mccabe](https://github.com/PyCQA/mccabe){:target="_blank"} which is the code complexity checker. `Flake8`{:.bash} is extendable through plugins. I would recommend the following plugins:

| Plugin      | Description |
| ----------- | ----------- |
| [flake8-bugbear](https://github.com/PyCQA/flake8-bugbear){:target="_blank"} | Finds bugs and design problems that are not covered by pyflakes and pycodestyle. Some warnings are disabled as controversial. So it is worth to check that list for any interesting that you could enable. My personal recommendation is to enable at least B904. It checks for [exception chaining](https://docs.python.org/3/tutorial/errors.html#exception-chaining){:target="_blank"} which improves maintainability of the code you are developing |
| [flake8-comprehensions](https://github.com/adamchainz/flake8-comprehensions){:target="_blank"} | Improves writing of list, dict and set comprehensions |
| [flake8-return](https://github.com/afonasev/flake8-return){:target="_blank"} | Improves function return statements by checking for inconsistent returns and possible optimization of branching logic used to return from the function |
| [flake8-quotes](https://github.com/zheller/flake8-quotes){:target="_blank"} | Improves consistency of quotes usage in your source code. You can configure double quotes or single quotes |
| [flake8-simplify](https://github.com/MartinThoma/flake8-simplify){:target="_blank"} | Improves maintainability of the code by suggesting simpler alternatives to some code constructs |
| [pep8-naming](https://github.com/PyCQA/pep8-naming){:target="_blank"} | Checks your code against [PEP 8](https://peps.python.org/pep-0008/){:target="_blank"} naming conventions. Install it if you follow PEP 8 code style recommendations, skip otherwise |
| [flake8-docstrings](https://pypi.org/project/flake8-docstrings/){:target="_blank"} | Checks for missing docstring comments in the code and issues in the existing docstring comments. |

`Flake8`{:.bash} should be installed as a development time tool into the development dependency group:

```bash
poetry add flake8 flake8-bugbear flake8-comprehensions flake8-return flake8-quotes flake8-simplify pep8-naming flake8-docstrings -G dev
```

`Flake8`{:.bash} supports different sources for its configuration but `pyproject.toml`{:.bash} file is not among them at the moment of the writing. Personally, I prefer to keep the `flake8`{:.bash} configuration in the dedicated file named `.flake8`{:.bash} which I should create in the package root directory. 

`Flake8`{:.bash} has some settings that are in conflict with `Black`{:.bash} by default. `Flake8`{:.bash} compatibility with `Black`{:.bash} can be turned on in the `.flake8`{:.bash} configuration file by adding the following configuration:

```ini
[flake8]
# Glob patterns to exclude certain files/directories from checks
exclude = .git,__pycache__,.venv,.vscode,.mypy_cache
# [flake8-quotes] Quotes style for (use 'single' if it works for you)
inline-quotes = double
# [flake8-docstrings] Convention for docstring comments
docstring-convention = google
# List of codes to ignore:
# D100 - ignore missing docstring on the public module level (flake8-docstrings)
# D104 - ignore missing docstring on the public package level (flake8-docstrings)
# D105 - ignore missing docstring in the magic methods (flake8-docstrings)
# D107 - ignore missing docstring in the __init__ method (flake8-docstrings)
# W503 - ignore line breaks before binary operators (flake8)
ignore = D100,D104,D105,D107,W503
# [flake8-bugbear] Use exception chaining when raising exception from inside the 'except' block
extend-select = B904
# synchronize flake on line length (Black sync on settings)
max-line-length = 119
# ignore the 'whitespace before ":"' warning (Black sync on settings)
extend-ignore = E203
```
> See [sample](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html){:target="_blank"} of 'google' docstring style. There are also ['numpy'](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html#example-numpy){:target="_blank"} and ['pep257'](https://peps.python.org/pep-0257/){:target="_blank"} docstring styles for your service.

> See [flake8-docstrings](https://www.pydocstyle.org/en/latest/error_codes.html){:target="_blank"} for the ignored codes D100, D104, D105, D107.

> See `flake8`{:.bash} [W503 rule](https://www.flake8rules.com/rules/W503.html){:target="_blank"} for the ignored code W503.

> Check the [flake8 integration](https://black.readthedocs.io/en/stable/guides/using_black_with_other_tools.html#flake8){:target="_blank"} in the `Black`{:.bash} documentation for details on `flake8`{:.bash} configuration options for `Black`{:.bash}.

Once installed and configured, `flake8`{:.bash} can be invoked by executing the following command in the root directory of the package:

```bash
flake8
```

`Flake8`{:.bash} supports ignoring rules for the specific lines of the code or for the entire file. To ignore specific errors in a code line you can end it with `# noqa: <comma-separated-rules>`{:.python} as in `# noqa: B008`{:.python}. Ignoring an entire file is as simple as placing `# flake8: noqa`{:.python} at the top of the file on a separate line.

# MyPy for Static Type Checking
[Mypy](https://github.com/python/mypy){:target="_blank"} is a static type checker for Python. `Mypy`{:.bash} analyzes type hints ([PEP 484](https://peps.python.org/pep-0484/#type-variables-with-an-upper-bound){:target="_blank"}) (also known as type annotations) you add to your program and produce errors when it finds that they are used incorrectly. `Mypy`{:.bash} doesn't run the code being checked but parses it, analyzes and produces any errors it finds. 

By default, `mypy`{:.bash} analyzes only statically typed code annotated with the type hints. All unannotated or dynamically typed code is skipped. Because of that, you can start using `mypy`{:.bash} to the existing projects with no type hints applied as it won't produce any errors. But as soon as you start adding type hints `mypy`{:.bash} starts working. You can change the default `mypy`{:.bash} behavior by [disallowing untyped definitions](https://mypy.readthedocs.io/en/stable/command_line.html#cmdoption-mypy-disallow-untyped-defs){:target="_blank"} deliberately, or even by switching `mypy`{:.bash} into a [strict mode](https://mypy.readthedocs.io/en/stable/command_line.html#cmdoption-mypy-strict){:target="_blank"} that enables all optional error checking flags.

As the type hints, `mypy`{:.bash} understands:
* Built-in types, like `int`{:.python}, `float`{:.python}, `list`{:.python}, `dict`{:.python}, etc.
* Types from the `typing`{:.python} package, like `Iterable`{:.python}, `Callable`{:.python}, `Any`{:.python}, etc.
* [Standard library types](https://docs.python.org/3/library/){:target="_blank"} through the stub files from the [typeshed](https://github.com/python/typeshed){:target="_blank"} project.
* User-defined classes and types, like `DatabaseConnection`{:.python} or `ProductDiscount`{:.python}, etc. you normally create, and any their subclasses.

`Mypy`{:.bash} can also do its best to infer types of variables in method bodies that don't have type hints specified.

`Mypy`{:.bash} documentation contains a [cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html#cheat-sheet-py3){:target="_blank"} for the quick introduction into which and how type hints can be applied in the most common scenarios.

`Mypy`{:.bash} should be installed as a development time tool into the development dependency group:

```bash
poetry add mypy -G dev
```

`Mypy`{:.bash} can be invoked from the command line for files or folders, packages, modules or a piece of code passed as a string. `Mypy`{:.bash} recursively runs in all subdirectories or for all sub-packages it finds in the roots passed in the command line. For example, to invoke `mypy`{:.bash} from the root package folder for 2 sub-packages developed in 2 separate subdirectories 'sub-package1' and 'sub-package2' in there just run:

```bash
mypy -p sub-package1 -p sub-package2
```
Check `mypy`{:.bash} [documentation](https://mypy.readthedocs.io/en/stable/running_mypy.html){:target="_blank"} for details on other invocation options.

By default, `mypy`{:.bash} looks for its configuration consequently in `mypy.ini`{:.bash}, `.mypy.ini`{:.bash}, `pyproject.toml`{:.bash} and `setup.cfg`{:.bash} files in the current directory, its [well-known directories](https://mypy.readthedocs.io/en/stable/config_file.html#the-mypy-configuration-file){:target="_blank"} or the user home directory. I prefer to turn on as much of static type checking as possible and disable rules only when it makes absolute sense for me. Global `mypy`{:.bash} configuration goes to section `[tool.mypy]`{:.ini} in the [`pyproject.toml`{:.bash}](https://mypy.readthedocs.io/en/stable/config_file.html#using-a-pyproject-toml-file){:target="_blank"} file. It can further be tweaked for individual modules in their own `[[tool.mypy.overrides]]`{:.ini} sections by specifying the module name as the value of the `module`{:.ini} parameter of that section. The `mypy`{:.bash} configuration I am using on daily basis is as follows:

```ini
[tool.mypy]
# Python version used to parse and check the target platform.
python_version = "3.10"
# Run mypy for all *.py files. 
# You may want to change it to run for the selected packages (then specify the packages in the Makefile as -p argument to mypy and remove the files parameter below)
files = "**/*.py"
# Enables all optional error checking flags
strict = "true"
# Produces a warning when returning a value with type Any from a function declared with a non-Any return type.
# The issue is that combining a typed variable with Any variable produces Any.
warn_return_any = "false"
# Use visually nicer output in error messages.
pretty = "true"
# A comma-separated list of mypy plugins. It is used to add types for 3rd party libraries.
# plugins = ""
```
The [`strict`{:.ini}](https://mypy.readthedocs.io/en/stable/config_file.html#confval-strict){:target="_blank"} mode may seem very restrictive at the beginning but you can configure it by disabling certain checks if you need. I would not also recommend to turn the `strict`{:.init} mode on from the beginning for existing large code bases. 

`Mypy`{:.bash} supports error suppressing for a line of a code, class or method, or entire file. To suppress errors for a line of a code just put the following inline comment at the end of the line: `# type: ignore`{:.python} to ignore all type errors on the line or `# type: ignore[error-code]`{:.python} to ignore specific error by its code. To suppress type errors for a class or a method decorate it with `@typing.no_type_check`{:.python} decorator. To suppress type errors for the entire file place `# mypy: ignore-errors`{:.python} at the top of the file.

# Centralized Tool Invocation with Make 
Now, we configured the set of developer's tools that assist us in following the best practices of Python programming. Currently each tool should be executed individually from the command line. The [GNU Make](https://www.gnu.org/software/make/){:target="_blank"} tool can be used for a Python project to keep the invocation of the tools simple and consistent. The common use of the `Make`{:.bash} is to build the packages from the source files. But it is not limited to only that. It can also be used to execute shell commands.

`Make`{:.bash} uses the makefile to get the knowledge which command to execute and how. For the purpose of executing a shell command, the makefile should contain rules that specifies targets. A target defines commands to invoke when the target is being executed by the `Make`{:.bash} tool. For our purposes, you should create a file named `Makefile`{:.bash} in the package root directory. The `Makefile`{:.bash} should contain the following content:

```yaml
# Default target to be executed when running make with no parameters.
.DEFAULT_GOAL := all
# Forces make to treat those targets as non-file targets
.PHONY: black flake mypy all

# Invoke black from the current directory when running the 'make black' target
black:
  black .

# Invoke flake8 from the current directory when running the 'make flake' target
flake:
  flake8

# Invoke mypy on all *.py files recursively in all subdirectories when running the 'make mypy' target
mypy:
  mypy

# Invoke 'black', 'flake' and 'mypy' targets one by one when running the 'make all' target
all: black flake mypy
```

Now, you can run formatter and linters by simply typing in the command line:

```bash
make
```
It will run the `black`{:.ini}, `flake`{:.ini} and `mypy`{:.ini} rules one by one. The `Make`{:.bash} stops at the first failed rule.

You can also run a specific pre-configured tool by providing the corresponding target to the `Make`{:.bash}. For example, to run `mypy`{:.bash} just type:

```bash
make mypy
```

This concept can be developed to further organize the development environment by including other tools, like running database migrations, deploying packages, etc.

# Conclusions
At this point you should have the ready-to-use reproducible development environment that will assist and support you in following the best practices of Python programming. To briefly recap:
1. You use `pyenv`{:.bash} to manage different versions of Python on your system. Now you may have with no hassle multiple different Python versions installed on the same machine which you can switch between with no burden.
2. You use the concept of virtual environments to create your local, isolated and reproducible development context. Now you can completely delete the virtual environment (your .venv folder) with all the installed packages and recreate it again using the same version of Python and packages. 
3. You manage all project dependencies with the `poetry`{:.bash}. You can add, update, remove and re-install packages you need for the development. You can organize them into the groups. You fixes their versions, so your teammates will have exactly the same dependencies, and you can reliably reproduce the same context while deploying, say, to production.
4. You use `Black`{:.bash} to format your code. If you are a part of the team, now your code and code written by other developers will look consistent. No need to get used to anyone's coding style.
5. You invoke `flake8`{:.bash} and `mypy`{:.bash} to apply best coding practices. Running them on a constant basis, for example, as a part of your Continuous Integration process or on a commit hook to Git repository, you add one more protection gateway to your development process and keep high the overall quality of your code base.
6. Finally, you rely on the `Make`{:.bash} tool to organize your development tool set. It further improves the consistency of the tool usage and helps to better maintain your development environment.

And the last but not least, I would suggest to consider this setup as the starting point that you can elaborate upon to shape your development environment the ways you find suitable exactly for your current project needs.

Finally, there is a [python-bootstrap repository](https://github.com/vglushko/python-bootstrap){:target="_blank"} on my GitHub that collects all the ideas from above in a single project. Feel free to use it as the starting point if you find it useful.