# Template for Python packaging and CI/CD

There are many tutorials online for `Python` packaging and dev-ops, but most of them only cover part of the picture. Discussion of the pros and cons of [different packaging tools](https://alpopkes.com/posts/python/packaging_tools/?utm_source=perplexity) and tech stack for python devops is not the concernt of this post, I only use this blog to remind myself one minimal functional template for my Python projects. 

[example repo](https://github.com/mzgsxs/ci-cd-test)
------
Tech stacks:
1. [pdm](https://pdm-project.org/en/latest/)
2. [keyring](https://github.com/jaraco/keyring)
3. [ruff](https://docs.astral.sh/ruff/) 
4. [pytest](https://docs.pytest.org/en/stable/index.html)
5. [sphinx](https://www.sphinx-doc.org/en/master/index.html)
6. [github-actions](https://docs.github.com/en/actions)

## Packaging and dependency and enviroment management (pdm)
Minimal structure required for packaging is as follows:
```
ROOT/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── PACKAGE_NAME/
│       ├── __init__.py
│       └── EXAMPLE.py
└── tests/
    ├── __init__.py
    └── test_EXAMPLE.py
```
Install (and use) or remove a specific python version
```
pdm python install 3.9.8
pdm python list
pdm python remove 3.9.8
```

[`pyproject.toml`](https://peps.python.org/pep-0621/) specifies the metadata/info for the project.
Start from inside empty `ROOT` directory, use pdm to automate this:
```
pdm init
```
[Add or remove dependencies/packages](https://pdm-project.org/en/latest/usage/dependency/) such as numpy by
```
pdm add DEPENDENCY_PACKAGE
pdm add DEV_DEPENDENCY_PACKAGE --group dev
pdm remove DEPENDENCY_PACKAGE
pdm remove DEV_DEPENDENCY_PACKAGE --group dev
```
[uv](https://docs.astral.sh/uv/) can be used as the package manager backend, but setup is [required](https://pdm-project.org/latest/usage/uv/). `pyproject.toml` will be automatically updated. To list packages install in `.venv` ( if not using [PEP 582](https://pdm-project.org/en/latest/usage/pep582/) )
```
pdm list
```
For version control, gitignore has also been generated automatically. 


## Security
If there is any authetication/sensitive infomation required for this package and repo is opensourced, we need a way to manage those keys. 
If the code need to be deployed in multiple machines/servers, I will probably need to use AWS Secrets Manager. 



## Lint & Formating
Ruff (Rust based, fast) can fix:
<ol>
  <li> import errors and sorting </li>
  <li> deprecated Syntax </li>
  <li> doc-strings formating </li>
  <li> Enforcing PEP8 Style </li>
  <li> ... </li>
</ol>

```
pdm add ruff --group dev
```
Manually add the following to `pyproject.toml`
```toml
[tool.ruff]
line-length = 88
exclude = ["build/", "docs/"]

[tool.ruff.lint]
extend-select = ["E501", "E", "F", "UP", "B", "SIM", "I"]
```
1. `E`: Pycodestyle errors (e.g., whitespace issues, PEP8 violations).
2. `F`: Pyflakes errors (e.g., undefined variables, unused imports).
3. `UP`: Rules for upgrading Python syntax (e.g., replacing deprecated syntax).
4. `B`: flake8-bugbear rules (e.g., detecting likely bugs or design issues).
5. `SIM`: flake8-simplify rules (e.g., suggesting simpler constructs for clarity).
6. `I`: Import-related rules (e.g., ensuring imports are sorted).
7. `E501` is a Pycodestyle rule that enforces a maximum line length limit.

And
```
pdm run ruff check --fix 
```
And
```
pdm run ruff format
```

### Testing
Test 
```
pdm add pytest --group dev
```
And
```
pdm run pytest
```


## Publishing
To publish to test pypi server
```
pdm build
pdm publish --no-build --repository testpypi --password PYPI_TOKEN
```
It's a twine wrapper and wheel file is really just a ZIP file


## Documentation

### Basics
Sphinx will be used, for it's cross-references feature & expandability
```
pdm add sphinx sphinx-autobuild --group dev
```
Init docs of `source` and `build` folder in `docs`, with `y` for `Separate source and build directories (y/n) [n]:` 
```
pdm run sphinx-quickstart docs
```
Build documentations by the below command, rerun it everytime after you make changes
```
pdm run sphinx-build -M html docs/source/ docs/build/
```
Add the following in the `extensions` section in `docs/conf.py` to allow automatic doc generation 
```python
extensions = [
...
    'sphinx.ext.autodoc',        # Automatically document code from docstrings
    'sphinx.ext.napoleon',       # Support for Google-style and NumPy-style docstrings
    'sphinx.ext.viewcode',       # Add links to source code in documentation
...
]
```
Generate rst API documents from the doc string in source code by 
```
pdm run sphinx-apidoc -o docs/source src/YOUR_PKG
```
Add generated rst files to `docs/source/index.rst`
```rst
Quick setup
-----------
install it using pip:

.. code-block:: console

   (.venv) $ pip install YOUR_PKG

Contents
--------
.. toctree::
    :maxdepth: 2
    :caption: YOUR CAPTION

    modules
```
Note that indentation is important!

### Directives
Make a new rst file in `docs/source` by
```
touch docs/source/installation.rst
```
Put the following inside this rst file
```rst
Installation
------------
How to install in macos ...
```
Add `installation` between `:caption: YOUR CAPTION` and `modules` in `docs/source/index.rst`

### Cross-references
References a specific document
```rst
This is a cross-reference to document :doc:`installation`
```
Label a specific section or block by
```rst
...

.. _my-label:

My section
----------

Blabla
...
```
Note there must be an empty line above `.. _my-label:`, it can be refered by 
```rst
This is another cross :ref:`my-labeltext <my-label>`
```
To refer a function/code
```rst
This is a reference to function :py:func:`YOUR_PKG.SUB_MODULE.FN`
```

### README
Either use rst for README or allow Sphinx to parse markdown files
```
pdm add myst-parser --group dev
```
And add the following to extensions in `conf.py`
```python
...
    'myst_parser',
...
```
Include `README.md` by adding this to `index.rst`
```rst
.. include:: ../../README.md
    :parser: myst_parser.sphinx_
```


## Notes
