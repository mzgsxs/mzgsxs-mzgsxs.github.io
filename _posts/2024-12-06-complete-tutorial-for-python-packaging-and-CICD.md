## complete-template-for-Python-packaging-and-CI/CD

There are many tutorials online for `Python` packageing and devops, but most of them only cover part of the picture. Discussion of the pros and cons of [different packaging tools](https://alpopkes.com/posts/python/packaging_tools/?utm_source=perplexity) and tech stack for python devops is not the concernt of this post, I only use this blog to remind myself one minimal functional template for my Python projects. 

[my example repo](https://github.com/mzgsxs/ci-cd-test)
---

### Components
Tech stacks:
1. pdm
2. keyring
3. 
4. 

#### Packaging, Publishing and dependency and enviroment management (pdm)
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
```
[uv](https://docs.astral.sh/uv/) can be used as the package manager backend, but setup is [required](https://pdm-project.org/latest/usage/uv/). `pyproject.toml` will be automatically updated. To list packages install in `.venv` ( if not using [PEP 582](https://pdm-project.org/en/latest/usage/pep582/) )
```bash
pdm list
```
For version control, gitignore has also been generated automatically. To publish to test pypi server
```bash
pdm publish --repository testpypi --password PYPI_TOKEN
```
It's a twine wrapper and wheel file is really just a ZIP file


#### Security
If there is any authetication/sensitive infomation required for this package and repo is opensourced, we need a way to manage those keys. 
If the code need to be deployed in multiple machines/servers, I will probably need to use AWS Secrets Manager. 



#### Lint


#### Testing


#### Publishing


#### Documentation

### Notes
