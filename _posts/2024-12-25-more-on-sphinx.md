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
