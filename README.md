# typer-app cookiecutter template

Cookiecutter template for creating new [Typer](https://typer.tiangolo.com) command-line tools.
Largely based on Simon Willison's [click-app](https://github.com/simonw/click-app) template.

## Installation

The following workflow is using [uv](https://docs.astral.sh/uv/). You can install `uv` like so:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
Thanks to `uvx` (comes with `uv`), you don't need to install `cookiecutter` to use it. All you need to do is:
```bash
uvx cookiecutter gh:lvg77/typer-app
```

This outputs a set of questions:
```
[1/6] app_name (): demo-app
[2/6] description (): Demo
[3/6] hyphenated (demo-app): 
[4/6] underscored (demo_app): 
[5/6] github_username (): lvg77
[6/6] author_name (): Lyubomir Georgiev
```

## Examples

Three examples of tools that were initially created using this template:

- [stocksim](https://github.com/LVG77/stocksim): A comand-line utility to simulate stock price over a future period
- [finwiz](https://github.com/lvg77/finwiz): A set of tools for investment analysis

## Usage

Run `uvx cookiecutter gh:lvg77/typer-app` and then answer the prompts. Here's an example run:
```
$ uvx cookiecutter gh:lvg77/typer-app
app_name []: my new tool
description []: Description of my new tool
hyphenated [my-new-tool]:
underscored [my_new_tool]:
github_username []: lvg77
author_name []: Lyubomir Georgiev
```
It is recommend to accept the suggested value for "hyphenated" and "underscored" by hitting enter on those prompts.

This will create a directory called `my-new-tool` - the tool name you enter is converted to lowercase and uses hyphens instead of spaces.


## Developing your command-line tool

Having created the new structure from the template, here's how to start working on the tool.

If your tool is called `my-new-tool`, you can start working on it like so:
```bash
cd my-new-tool
```

The `[project.optional-dependencies]` section of the `pyproject.toml` lists all test dependecies. You can create a new virtual environment in .venv/ and install both your project dependencies and those test dependencies like this:
```bash
uv sync --extra test
```

### Running the tests

Now you can run pytest using the uv run command:
```bash
uv run pytest
```

### Running the CLI tool

This line in `pyproject.toml` defines a script entry point for the CLI tool:

```toml
[project.scripts]
demo-app = "my_new_tool.cli:app"
```
If the tool is correctly installed, you should be able to run it like this:
```bash
uv run my-new-tool
```
You can also run it via Python like this (producing the same output):
```bash
uv run python -m demo_app
```

At that point the tool will be installed in edit mode. The reason why this works is that `pyproject.toml` includes the following build-system section:
```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"
```

The only reason you needed to use `uv sync` here was to specify that `--extra test` to get the test dependencies installed as well.

As an aside, the following would have worked instead:
```bash
uv run --extra test pytest
```
You only need to pass that `--extra test` option the first time you ran `uv run` - on subsequent runs the test dependencies would already be installed.


Now you can open the `my_new_tool/cli.py` file and start adding Typer [arguments, options,commands and groups](https://typer.tiangolo.com/tutorial/arguments/).

## Creating a Git repository for your tool

You can initialize a Git repository for your tool like this:
```bash
cd my-new-tool
git init
git add .
git commit -m "Initial structure from template"
# Rename the 'master' branch to 'main':
git branch -m master main
```
## Publishing your tool to GitHub

Use https://github.com/new to create a new GitHub repository sharing the same name as your tool, which should be something like `my-new-tool`.

Push your `main` branch to GitHub like this:
```bash
git remote add origin git@github.com:YOURNAME/my-new-tool.git
git push -u origin main
```
The template will have created a GitHub Action which runs your tool's test suite against every commit.

## Publishing your tool as a package to PyPI

The template also includes a `publish.yml` GitHub Actions workflow for publishing packages to [PyPI](https://pypi.org/), using [pypa/gh-action-pypi-publish](https://github.com/pypa/gh-action-pypi-publish).

To use this action, you need to create a PyPI account and [configure a Trusted Publisher](https://til.simonwillison.net/pypi/pypi-releases-from-github) for this package.

Once you have created your account, navigate to https://pypi.org/manage/account/publishing/ and create a "pending publisher" for the package. Use the following values:

- **PyPI Project Name:** The name of your package
- **Owner:** Your GitHub username or organization - the "foo" in `github.com/foo/bar`
- **Repsitory name:** The name of your repository - the "bar" in `github.com/foo/bar`
- **Workflow name:** `publish.yml`
- **Environment name:** `release`

Now, any time you create a new "Release" on GitHub the Action will build your package and push it to PyPI.

The tag for your release needs to match the `VERSION` string at the top of your `pyproject.toml` file. You should bump this version any time you release a new version of your package.
