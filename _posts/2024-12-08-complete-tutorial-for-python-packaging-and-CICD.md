## Complete-template-for-Python-packaging-and-CI/CD

There are many tutorials online for `Python` packageing and devops, but most of them only cover part of the picture. Discussion of the pros and cons of different tech stack for python devops is not the concernt of this post, I only use this blog to remaind myself one minimal functional template for my Python projects. 

[my example repo](https://github.com/mzgsxs/ci-cd-test)
---

### Components
Tech stacks:
1. ruff
2. isort
3. pytest
4. poetry

#### Packaging
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
    └── test_EXAMPLE.py
```
`pyproject.toml` specifies the metadata/info for the project, I use poetry to automate it.


#### Lint


#### Testing


#### Publishing


### Notes
