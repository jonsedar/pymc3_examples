**Jon Sedar Personal Project**

# PyMC3 Examples `pymc3_examples`

##### 2019Q4

Experimental models to demonstrate `pymc3` capabilities. 

Where suitable, I will submit these Notebooks for inclusion in the 
[pymc3 documentation](https://docs.pymc.io/nb_examples/index.html)

Note: 

+ Each Notebook is designed to be standalone with a suggested MVP 
condaenv install, but you can also just install the env for this project.
+ This project is hosted publicly on 
[GitHub](https://github.com/jonsedar/pymc3_examples).
+ This README is MacOS oriented

---

# 1. Setup Development Environment

## 1.1 Libraries, Compilers, IDEs

### 1.1.1 Continuum Anaconda Python 64bit 

This repo uses Python 3.6.*

Download Anaconda distro at [https://www.continuum.io/downloads](https://www.continuum.io/downloads)

### 1.1.2 C++ compiler

Theano devs recommend using `clang` (aka `clang++`) by default which you can install using Xcode Command Line Tools

```zsh
$> xcode-select --install
```

It's also possible to use g++ (gcc), but in my experience that's about 1/2 the speed.

e.g. install via Homebrew

```zsh
$> brew update && brew upgrade `brew outdated` 
$> brew install gcc
```

...optional cleanup of old versions afterwards

```zsh
$> brew cleanup
```

Note:

1. By default `gcc` is symlinked / points to `clang` on MacOS
2. It _is_ possible to install a particular version of `gcc` via Homebrew, but this gets installed to a versioned dir e.g. `/usr/local/Cellar/gcc/9.3.0_1/bin/gcc-9` and by default is not symlinked from `gcc`, rather it is available as `gcc-9`
3. Regardless, it is best to use `clang` because theano is/was developed to this and is optimized (much faster)
4. Note you then don't need to configure the cxx in `.theanorc`, it uses `clang` by default

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

```zsh
$> jupyter notebook --generate-config
$> jupyter qtconsole --generate-config
$> jupyter nbconvert --generate-config
```

## 1.3 Clone Code and Create Environment

### 1.3.1 Git clone the repo to your workspace

```zsh
$> git clone https://github.com/jonsedar/pymc3_examples.git
$> cd pymc3_examples/
```

### 1.3.2 Setup a virtual environment for Python libraries

**IMPORTANT NOTE:** We're not using the latest python here because we want 
stability for the `pymc3` and `theano` installs.

Pay extra attention to the `.theanorc` config

```zsh
$> conda env create --file condaenv_pymc3_examples.yml
$> activate pymc3_examples
```

Cheat sheet of conda commands available 
[online here](https://conda.io/docs/_downloads/conda-cheatsheet.pdf).

#### b. Additional packages via pip

```zsh
$> /.pip_install.sh
```

#### c. Additional jupyterlab theme

Requires [NodeJS](https://nodejs.org/en/)

```zsh
$> jupyter labextension install @telamonian/theme-darcula
```

### 1.3.3 Test installation of scientific packages

A quick and cheap way to confirm your binaries are good and the 
installation works!

#### 1.3.3.1 Test BLAS / MKL config

View the BLAS / MKL install 

``` zsh
$> python -c "import numpy as np; np.__config__.show()"
```

Example output...

```zsh
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

``` zsh
$> python -c "import numpy as np; np.test()"
```

Example output...

```zsh
7285 passed, 80 skipped, 167 deselected, 12 xfailed, 3 xpassed, 2 warnings in 355.51 seconds
```

#### 1.3.3.3 Test `scipy` install

``` zsh
$> python -c "import scipy as sp; sp.test()"
... many tests will run and output a results summary e.g. ...
```

Example output...

```zsh
14522 passed, 1274 skipped, 1225 deselected, 79 xfailed, 10 xpassed, 25 warnings in 777.53 seconds 
```

#### 1.3.3.4 OPTIONAL Test `pymc3` install

Method 1:

```zsh
$> python -m pytest -xv --cov=pymc3 --cov-report=html pymc3/
```

Method 2: 

Takes ~90mins on my Macbook 2017 :/

``` zsh
$> python -c "import pymc3 as pm; pm.test()"
```

Example output...

```zsh
TBD
```

#### 1.3.3.5 OPTIONAL Test `theano` install

Takes forever, see [installation docs](http://deeplearning.net/software/theano/install_macos.html)

Quicker:

``` zsh
$> python -c "import theano; theano.test()"
```

Alternative (takes ~3 hours)

``` zsh
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

``` zsh
$> conda clean -v -a
```

NOTE @2019-01-19 there's an [open bug](https://github.com/conda/conda/pull/7149) in `conda` on Windows that the `~/AppData/Local/Continuum/Anaconda3/pkgs/.trash` directory needs manual emptying afterwards

### 1.4.3 Specify number of CPUs for MKL to use

Ought to provide a speed-boost for MKL operations (many numpy, scipy operations under the hood). See  [docs](https://docs.anaconda.com/mkl-service/)

```zsh
$> python -c "import mkl; mkl.set_num_threads(4); print(mkl.get_max_threads())
4
```

### 1.4.4 MVP Jupyter Widget

Interactive widgets are cool. Beware potential issue with lax requirements. Ensure `conda_*.yaml` contains correct pairs of :

```yaml
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

---

**Jonathan Sedar &copy; 2020**