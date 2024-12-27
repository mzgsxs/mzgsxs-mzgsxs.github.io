# More on sphinx


```
cd docs
pdm run make clean html
cd ..
```


With the default theme `alabaster`, you should have the following in `conf.py`
```
html_sidebars = {
    '**': [
        'about.html',
    ]
}
```

Add the following to `docs/conf.py` 
```python
# Path to your Python code relative to the `docs/` directory
import os
import sys
sys.path.insert(0, os.path.abspath('../src/YOUR_PKG'))
```


Automatically rebuild and serve your docs localy while developing/editing
```
pdm run sphinx-autobuild docs docs/_build/html
```

## Classical publishing
Classical way blishing the documentation website. First you have to makesure `Settings` -> `Code and automation` -> `Actions` -> `General` -> `Workflow permissions` -> `Read and write permissions` is selected and saved. Then use this [github action file](https://github.com/mzgsxs/ci-cd-test/blob/main/.github/workflows/gh-pages-classical.yml) (user guide [here](https://github.com/peaceiris/actions-gh-pages#readme)) to automate building, by saving it in `.github/workflows/`. After pushing to the main branch, under `Settings` -> `Code and automation` -> `Pages`, `Build and deployment` -> `Source`, select `Deploy from a branch` and then branch `gh-pages` and `/(root)`.
