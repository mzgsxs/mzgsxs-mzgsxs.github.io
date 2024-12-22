## Template for Python packaging and CI/CD

There are many tutorials online for `Python` packageing and devops, but most of them only cover part of the picture. Discussion of the pros and cons of [different packaging tools](https://alpopkes.com/posts/python/packaging_tools/?utm_source=perplexity) and tech stack for python devops is not the concernt of this post, I only use this blog to remind myself one minimal functional template for my Python projects. 

[my example repo](https://github.com/mzgsxs/ci-cd-test)
---

### Components
Tech stacks:
1. pdm
2. keyring
3. ruff
4. pytest
5. 

#### Packaging and dependency and enviroment management (pdm)
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
```bash
pdm init
```
[Add or remove dependencies/packages](https://pdm-project.org/en/latest/usage/dependency/) such as numpy by
```bash
pdm add DEPENDENCY_PACKAGE
pdm add DEV_DEPENDENCY_PACKAGE --group dev
pdm remove DEPENDENCY_PACKAGE
pdm remove DEV_DEPENDENCY_PACKAGE --group dev
```
[uv](https://docs.astral.sh/uv/) can be used as the package manager backend, but setup is [required](https://pdm-project.org/latest/usage/uv/). `pyproject.toml` will be automatically updated. To list packages install in `.venv` ( if not using [PEP 582](https://pdm-project.org/en/latest/usage/pep582/) )
```bash
pdm list
```
For version control, gitignore has also been generated automatically. 


#### Security
If there is any authetication/sensitive infomation required for this package and repo is opensourced, we need a way to manage those keys. 
If the code need to be deployed in multiple machines/servers, I will probably need to use AWS Secrets Manager. 



#### Lint & Formating
Ruff (Rust based, fast) can fix:
<ol>
  <li> import errors and sorting </li>
  <li> deprecated Syntax </li>
  <li> doc-strings formating </li>
  <li> ... </li>
</ol>

```
pdm add ruff --group dev
```
Manually add the following to `pyproject.toml`
```
[tool.ruff]
line-length = 88
exclude = ["build/", "docs/"]
select = ["E", "F", "UP", "B", "SIM", "I"]

[tool.ruff.lint]
extend-select = ["E501"]
```
<ol>
  <li>`E`: Pycodestyle errors (e.g., whitespace issues, PEP8 violations).
  <li>`F`: Pyflakes errors (e.g., undefined variables, unused imports).
  <li>`UP`: Rules for upgrading Python syntax (e.g., replacing deprecated syntax).
  <li>`B`: flake8-bugbear rules (e.g., detecting likely bugs or design issues).
  <li>`SIM`: flake8-simplify rules (e.g., suggesting simpler constructs for clarity).
  <li>`I`: Import-related rules (e.g., ensuring imports are sorted).
  <li>`E501` is a Pycodestyle rule that enforces a maximum line length limit.
</ol>

```
pdm run ruff check --fix 
```

```
pdm run ruff format
```

#### Testing
```
pdm add pytest --group dev
```

#### Publishing
To publish to test pypi server
```bash
pdm build
pdm publish --no-build --repository testpypi --password PYPI_TOKEN
```
It's a twine wrapper and wheel file is really just a ZIP file

#### Documentation

### Notes
