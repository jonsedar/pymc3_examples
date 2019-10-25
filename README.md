**Jon Sedar Personal Project**

# PyMC3 Examples `pymc3_examples`

##### 2019Q4

Experimental models to demonstrate `pymc3` capabilities. 

Where suitable, I will submit these Notebooks for inclusion in the 
[pymc3 documentation](https://docs.pymc.io/nb_examples/index.html)


Note: 
+ Each Notebook is designed to be standalone with a suggested MVP 
condaenv install, but you can also just install the env for this project.
+ This project is hosted publically on 
[GitHub](https://github.com/jonsedar/pymc3_examples).
+ This README is MacOS oriented

---

# 1. Setup Development Environment


## 1.1 Libraries, Compilers, IDEs

### 1.1.1 Continuum Anaconda Python 3.6.* 64bit 

Main site: [https://www.continuum.io/downloads](https://www.continuum.io/downloads)


### 1.1.2 C++ compiler

e.g. for Mac OSX using Homebrew

```bash
$> brew update && brew upgrade `brew outdated` 
$> brew install gcc
```

...optional cleanup of old versions afterwards

```bash
$> brew cleanup
```


## 1.2 Configs, Dotfiles

### 1.2.1 Global git config `~/.gitconfig`

``` yaml
[user]
	name = $YOUR_USERNAME
	email = $YOUR_EMAIL
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
```


### 1.2.2 Theano config `~/.theanorc`

``` yaml
[global]
device=cpu
```


### 1.2.3 Optional: jupyter configs if not already setup

```bash
$> jupyter notebook --generate-config
$> jupyter qtconsole --generate-config
$> jupyter nbconvert --generate-config
```

## 1.3 Clone Code and Create Environment


### 1.3.1 Git clone the repo to your workspace.


```bash
$> git clone https://github.com/jonsedar/pymc3_examples.git
$> cd pymc3_examples/
```


### 1.3.2 Setup a virtual environment for Python libraries

**IMPORTANT NOTE:** We're not using the latest python here because we want 
stability for the `pymc3` and `theano` installs.

Pay extra attention to the `.theanorc` config


```bash
$> conda env create --file condaenv_pymc3_examples.yml
$> activate pymc3_examples
```

Cheat sheet of conda commands available 
[online here](https://conda.io/docs/_downloads/conda-cheatsheet.pdf).



### 1.3.3 Test installation of scientific packages

A quick and cheap way to confirm your binaries are good and the 
installation works!

#### 1.3.3.1 BLAS / MKL config

View the BLAS / MKL install 

``` bash
$> python -c "import numpy as np; np.__config__.show()"
```

Output example...
```bash
mkl_info:
    libraries = ['mkl_rt', 'pthread']
    library_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/lib']
    define_macros = [('SCIPY_MKL_H', None), ('HAVE_CBLAS', None)]
    include_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/include']
blas_mkl_info:
    libraries = ['mkl_rt', 'pthread']
    library_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/lib']
    define_macros = [('SCIPY_MKL_H', None), ('HAVE_CBLAS', None)]
    include_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/include']
blas_opt_info:
    libraries = ['mkl_rt', 'pthread']
    library_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/lib']
    define_macros = [('SCIPY_MKL_H', None), ('HAVE_CBLAS', None)]
    include_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/include']
lapack_mkl_info:
    libraries = ['mkl_rt', 'pthread']
    library_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/lib']
    define_macros = [('SCIPY_MKL_H', None), ('HAVE_CBLAS', None)]
    include_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/include']
lapack_opt_info:
    libraries = ['mkl_rt', 'pthread']
    library_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/lib']
    define_macros = [('SCIPY_MKL_H', None), ('HAVE_CBLAS', None)]
    include_dirs = ['/Users/jon/anaconda/envs/pymc3_examples/include']
```


#### 1.3.3.2 Test `numpy` install

``` bash
$> python -c "import numpy as np; np.test()"
```

Example output...

```bash
7285 passed, 80 skipped, 167 deselected, 12 xfailed, 3 xpassed, 2 warnings in 355.51 seconds
```

#### 1.3.3.3 Test `scipy` install

``` bash
$> python -c "import scipy as sp; sp.test()"
... many tests will run and output a results summary e.g. ...
```

Example output...
```bash
14522 passed, 1274 skipped, 1225 deselected, 79 xfailed, 10 xpassed, 25 warnings in 777.53 seconds 
```


#### 1.3.3.4 OPTIONAL Test `pymc3` install


Method 1:

```bash
$> python -m pytest -xv --cov=pymc3 --cov-report=html pymc3/
```


Method 2: 

Takes ~90mins on my Macbook 2017 :/

``` bash
$> python -c "import pymc3 as pm; pm.test()"
```

Example output...
```bash

```


#### 1.3.3.5 OPTIONAL Test `theano` install

Takes forever, see [installation docs](http://deeplearning.net/software/theano/install_macos.html)

Quicker:
``` bash
$> python -c "import theano; theano.test()"
```

Alternative (takes ~3 hours)
``` bash
$> theano-nose -s
... many tests will run and output a results summary e.g. ...
```


## 1.4 General Python Dev - Useful stuff

### 1.4.1 List large objects currently consuming RAM

As per [https://stackoverflow.com/questions/40993626/list-memory-usage-in-ipython-and-jupyter](https://stackoverflow.com/questions/40993626/list-memory-usage-in-ipython-and-jupyter)

``` python
import sys

# These are the usual ipython objects, including this one you are creating
ipython_vars = ['In', 'Out', 'exit', 'quit', 'get_ipython', 'ipython_vars']

# Get a sorted list of the objects and their sizes
sorted([(x, sys.getsizeof(globals().get(x))) for x in dir() if not x.startswith('_') and x not in sys.modules and x not in ipython_vars], key=lambda x: x[1], reverse=True)
```

### 1.4.2 Clean up conda packages

Remove unused packages and tarballs etc as explained [https://conda.io/docs/commands/conda-clean.html](https://conda.io/docs/commands/conda-clean.html)

``` bash
$> conda clean -v -a
```
NOTE @2019-01-19 there's an [open bug](https://github.com/conda/conda/pull/7149) in `conda` on Windows that the `~/AppData/Local/Continuum/Anaconda3/pkgs/.trash` directory needs manual emptying afterwards


### 1.4.3 Specify number of CPUs for MKL to use

Ought to provide a speed-boost for MKL operations (many numpy, scipy operations under the hood). See  [docs](https://docs.anaconda.com/mkl-service/)

```
>>> import mkl
>>> mkl.set_num_threads(2)
>>> mkl.get_max_threads()
2
```

### 1.4.4 MVP Jupyter Widget

Interactive widgets are cool. Beware potential issue with lax requirements. Ensure `conda_*.yaml` contains correct pairs of :

```
- ipywidgets==6.0.0
- widgetsnbextension==2.*
```

```python
from ipywidgets import widgets
from IPython.display import display
text = widgets.Text()
text.on_submit(lambda x: print('hello {}'.format(x.value)))
display(text)
```

### 1.4.5 Basic debugging / introspection 

Straight breakpoint:

```python
import pdb; pdb.set_trace()

```

Or print, often helpful if you can't set a breakpoint

```python
for att in dir(myobject):
    print(att, getattr(myobject, att))
```

---


# Data

See `data/README_DATA.md`

---

# General Notes

Any project applicable notes should go in the 00_Overview.ipynb file.

---

**Jonathan Sedar &copy; 2019**