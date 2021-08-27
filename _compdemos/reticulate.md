---
title: Using reticulate with Environment Modules
last_modified_at: 2021-08-27
primary_reviewers: atombaby
---

_reticulate_ is a library that allows R and Python to inter-operate:

> The reticulate package provides a comprehensive set of tools for interoperability between Python and R.

> Reticulate embeds a Python session within your R session, enabling seamless, high-performance interoperability. If you are an R developer that uses Python for some of your work or a member of data science team that uses both languages, reticulate can dramatically streamline your workflow!

Using _reticulate_ with the Python and R modules works well, but there are a few things to note when setting this up.

## Which Python?

The python that _reticulate_ actually uses may not be the one you want it to use.  Without changes, there is a [set of heuristics](https://rstudio.github.io/reticulate/articles/versions.html) that the library uses to select the Python installation used.  There are a few different ways to adjust this algorithm:

 - R functions `use_python`, `use_virtualenv`, and `use_condaenv`
 - the `RETICULATE_PYTHON` environment variable

The functions are advisory, providing hints to _reticulate_ as to where to find a Python version. These do support a `required` option which will raise an error if Python is not found

## Using reticulate with Modules

While dynamic algorithms to locate Python can be useful, in actual practice it may be preferable to enforce the Python installation used.  The `RETICULATE_PYTHON` environment variable provides a clear way to do this outside the core logic of your code.  To use, set this environment variable to the full path to the Python executable:

```sh
$ ml Python/3.8.6-GCCcore-10.2.0
$ which python
/app/software/Python/3.8.6-GCCcore-10.2.0/bin/python
$ export RETICULATE_PYTHON=$(which python)
$ echo $RETICULATE_PYTHON
/app/software/Python/3.8.6-GCCcore-10.2.0/bin/python
```

## Using reticulate with Virtual Environments

When using Python virtual environments, load the necessary Python version with `ml` and activate the environment.  Then use `RETICULATE_PYTHON` as above:

```sh
$ ml Python/3.8.6-GCCcore-10.2.0
$ . ./myproject/bin/activate
(myproject) $ which python
/home/myproject/bin/python
(myproject) $ export RETICULATE_PYTHON=$(which python)
(myproject) $ echo $RETICULATE_PYTHON 
/home/myproject/bin/python
```

## Notes

### Python Selection and Numpy

The functions `py_config` and `py_discover_config` will load the `numpy` library in python.  If the desired python installation does not have `numpy` installed, these functions will not use it without forcing the installation path.

The basic Python modules (the ones named "Python") don't have `numpy` installed so may not appear when using those functions.  Similarly, if you create a virtual environment that does not contain the `numpy` library it will also not be used by these functions.

> TBD: If your code does not call either of these functions, will it use a Python installation that doesn't have `numpy`?

