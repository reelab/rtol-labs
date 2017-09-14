# Installing and Running Jupyter

These days, scientific data analysis is increasingly done with open-source software (i.e., freely available to use and modify), especially in high-level programming languages such as R and Python. For our labs, we will use [Jupyter](https://jupyter.org), a web application with "kernels" for executing code in various languages, and [Jupyter notebooks](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html), which allow you to organize "cells" of documentation, source code, and the results of code execution into self-contained, self-explanatory documents.

Our lab exercises are written as Jupyter notebooks. To open these files, we need to install and run Jupyter.

## 1 Install the Miniconda Python 3 distribution

Jupyter is written in Python and we will use Python in our notebooks. So, the first step is to install Python. While Python comes pre-installed on Mac and Linux systems, we will use [Miniconda](https://conda.io/miniconda.html), which is a separate installation of Python that is private to you (so you could repeat the process below on your personal computer, a shared Linux system, etc. It's a good way to install Python on Windows.) Miniconda includes [conda](https://conda.io/docs), a system that makes it easy to install a wide range of open-source libraries (a.k.a. modules or packages) available for scientific analysis.

In a web browser, navigate to <https://conda.io/miniconda.html> and download the Python 3.6 Mac OS X 64-bit bash installer, or use this [direct link](https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh).

Open a Terminal and run the installer (assuming it is now in the `Downloads` folder):

```bash
bash Downloads/Miniconda3-latest-MacOSX-x86_64.sh
```

Press `space` to page through to the end of the agreement and type `yes`. Then answer yes (type it or just hit enter) at the following:

```
Do you wish the installer to prepend the Miniconda3 install location
to PATH in your /Users/<you>/.bash_profile ? [yes|no]
[yes] >>>
```

Close the Terminal.

You now have a basic Python 3 installation. To use it, you should create a new **environment** -- a "sandbox" into which you can install just the packages you need for a particular task. This way, you can make different environments for different projects.

Create an environment to use throughout the quarter -- let's call it `rtol`. Open a new Terminal and type:

```bash
conda create -n rtol jupyter biopython r-irkernel
```

This creates the `rtol` environment and installs into it the `jupyter`, `biopython`, and `r-irkernel` packages (and their dependencies). To use the environment it must be activated:

```bash
source activate rtol
```

Your Terminal prompt should now begin with `(rtol)`.

## Run Jupyter

In the same Terminal, run:

```bash
jupyter-notebook
```

This will open a browser window showing files and folders in the current directory. Jupyter notebook files are conventionally named with an `.ipynb` extension, reflecting their derivation from IPython notebooks.

You are now ready to use the lab notebooks in this repository.
