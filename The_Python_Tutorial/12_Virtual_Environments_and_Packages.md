[Go Back](./README.md)

# <a name="12"></a> 12. Virtual Environments and Packages

## <a name="12_1"></a> 12.1. Introduction

Python applications will often use packages and modules that don’t come as part of the standard library. Applications will sometimes need a specific version of a library, because the application may require that a particular bug has been fixed or the application may be written using an obsolete version of the library’s interface.

This means it may not be possible for one Python installation to meet the requirements of every application. If application A needs version 1.0 of a particular module but application B needs version 2.0, then the requirements are in conflict and installing either version 1.0 or 2.0 will leave one application unable to run.

The solution for this problem is to create a **virtual environment**, _a self-contained directory tree that contains a Python installation for a particular version of Python_, plus a number of additional packages.

Different applications can then use different virtual environments. To resolve the earlier example of conflicting requirements, application A can have its own virtual environment with version 1.0 installed while application B has another virtual environment with version 2.0. If application B requires a library be upgraded to version 3.0, this will not affect application A’s environment.

## <a name="12_2"></a> 12.2. Creating Virtual Environments

The module used to create and manage virtual environments is called ``venv``. ``venv`` will usually install the most recent version of Python that you have available. If you have multiple versions of Python on your system, you can select a specific Python version by running python3 or whichever version you want.

To create a virtual environment, decide upon a directory where you want to place it, and run the ``venv`` module as a script with the directory path:

```
python3 -m venv tutorial-env
```

This will create the _tutorial-env_ directory if it doesn’t exist, and also create directories inside it containing a copy of the Python interpreter, the standard library, and various supporting files.

Once you’ve created a virtual environment, you may activate it.

```
On Windows, run:
    tutorial-env\Scripts\activate.bat

On Unix or MacOS, run:
    source tutorial-env/bin/activate
```

(This script is written for the _bash shell_. If you use the csh or fish shells, there are alternate activate.csh and activate.fish scripts you should use instead.)

Activating the virtual environment will change your shell’s prompt to show what virtual environment you’re using, and modify the environment so that running python will get you that particular version and installation of Python. For example:

```python
$ source ~/envs/tutorial-env/bin/activate
(tutorial-env) $ python
Python 3.5.1 (default, May  6 2016, 10:59:36)
  ...
>>> import sys
>>> sys.path
['', '/usr/local/lib/python35.zip', ...,
'~/envs/tutorial-env/lib/python3.5/site-packages']
>>>
```

## <a name="12_3"></a> 12.3. Managing Packages with pip

You can install, upgrade, and remove packages using a program called ``pip``. By default pip will install packages from the _Python Package Index_, [https://pypi.org](https://pypi.org).

pip has a number of subcommands: “search”, “install”, “uninstall”, “freeze”, etc.

You can install the latest version of a package by specifying a package’s name:
```
(tutorial-env) $ pip install novas
Collecting novas
  Downloading novas-3.1.1.3.tar.gz (136kB)
Installing collected packages: novas
  Running setup.py install for novas
Successfully installed novas-3.1.1.3
```

You can also install a specific version of a package by giving the package name followed by ``==`` and the version number:
```
(tutorial-env) $ pip install requests==2.6.0
Collecting requests==2.6.0
  Using cached requests-2.6.0-py2.py3-none-any.whl
Installing collected packages: requests
Successfully installed requests-2.6.0
```

``pip uninstall`` followed by one or more package names will remove the packages from the virtual environment.

``pip show`` will display information about a particular package.

``pip list`` will display all of the packages installed in the virtual environment.
