## Complete-template-for-Python-packaging-and-CI/CD

There are many tutorials online for `Python` packageing and devops, but most of them only cover part of the picture. I use this blog to note one minimal template for my Python projects.

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
root/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── package_name/
│       ├── __init__.py
│       └── example.py
└── tests/
```
`pyproject.toml` specifies the metainfo for the project, I use poetry to automate it.


#### Lint


#### Testing


#### Publishing


### Notes
