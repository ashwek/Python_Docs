[Go Back](./README.md)

# <a name="6"></a> 6. Modules

As program gets longer, we may want to split it into several files for easier maintenance. We may also want to use a handy function that we’ve written in several programs without copying its definition into each program.

To support this, Python has a way to put definitions in a file and use them in a script or in an interactive instance of the interpreter. Such a file is called a **module**; definitions from a module can be _imported_ into other modules.

**Within a module**, the module’s name is available as the value of the global variable ```__name__```.

```python3
import some_module
some_module.__name__
```

Importing does not enter the names of the functions defined in ```some_module``` directly in the current symbol table; it only enters the module name ```some_module``` there.

## <a name="6_1"></a> 6.1. More on Modules

A module can contain executable statements _(global statements outside any function)_ as well as function definitions. These statements are intended to initialize the module. They are _executed only the first time the module name is encountered in an import statement_.

_Each module has its own private symbol table_, which is used as the global symbol table by all functions defined in the module. Thus, the author of a module can use global variables in the module without worrying about accidental clashes with a user’s global variables.

There is a variant of the ```import``` statement that imports names from a module **directly into the importing module’s symbol table**. This does not introduce the module name from which the imports are taken in the local symbol table.

```python3
>>> from os import chdir
>>> chdir("../path/..")
```

There is even a variant to **import all names** that a module defines. This **imports all names except those beginning with an underscore**. In most cases Python programmers do not use this facility since it introduces an unknown set of names into the interpreter, possibly hiding some things you have already defined.

```python3
>>> from os import *
>>> chdir("../path/..")
```

If the module name is followed by ```as```, then the name following ```as``` is bound directly to the imported module.

```python3
>>> from os import chdir as cd
>>> cd("../path/..")
```

### <a name="6_1_1"></a> 6.1.1. Executing modules as scripts

If we run a Python module the module will be executed, just as if we imported it, but with the ```__name__``` set to **"\_\_main\_\_"**. This is often used either to provide a convenient user interface to a module, or for testing purposes.

```python3
if __name__ == "__main__":
    print("Executed as a script")
else:
    print("Imported as a module")
```

### <a name="6_1_2"></a> 6.1.2. The Module Search Path

When a module named **xyz** is imported, the interpreter first searches for a built-in module with that name. If not found, it then searches for a file named **xzy.py** in a list of directories given by the variable ```sys.path```. ```sys.path``` is initialized from these locations:
 - The directory containing the input script (or the current directory when no file is specified)
 - **PYTHONPATH** (a list of directory names, with the same syntax as the shell variable PATH)
 - The installation-dependent default.

After initialization, **Python programs can modify ```sys.path```**. The directory containing the script being run is placed at the beginning of the search path, ahead of the standard library path. This means that **scripts in that directory will be loaded instead of modules of the same name in the library directory**.

We can modify ```sys.path``` using standard list operations:

```python3
>>> import sys
>>> sys.path.append('../path/..')
```

### <a name="6_1_3"></a> 6.1.3. “Compiled” Python files

To speed up loading modules, Python **caches the compiled version of each module** in the ```__pycache__``` directory under the name **module.version.pyc**, where the version encodes the format of the compiled file; it generally contains the Python version number.

For example, in _CPython release 3.3_ the compiled version would be cached as __pycache__/file_name.cpython-33.pyc. This naming convention allows compiled modules from different releases and different versions of Python to coexist.

_Python checks the modification date of the source against the compiled version to see if it’s out of date and needs to be recompiled._ Also, the **compiled modules are platform-independent**, so the same library can be shared among systems with different architectures.

_Python does not check the cache in two circumstances_.
- it always recompiles and does not store the result for the module that’s loaded directly from the command line.
- it does not check the cache if there is no source module. _To support a non-source (compiled only) distribution, the compiled module must be in the source directory, and there must not be a source module._

#### **A program doesn’t run any faster when it is read from a .pyc file than when it is read from a .py file; the only thing that’s faster about .pyc files is the speed with which they are loaded.**

## <a name="6_2"></a> 6.2. Standard Modules

Python comes with a library of standard modules. Some modules are built into the interpreter; these provide access to operations that are not part of the core of the language but are nevertheless built in. The set of such modules is a configuration option which also depends on the underlying platform.

## <a name="6_3"></a> 6.3. The dir() Function

The **built-in function** ``dir()`` is used to find out which **names a module defines**. It returns a sorted list of strings.
Without arguments, ```dir()``` lists the names _(variables, modules, functions, etc)_ you have defined currently.

```python3
>>> import os
>>> dir(os)
['CLD_CONTINUED', ..., 'writev']
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'os']
```

To list the names of built-in functions and variables:

```python3
>>> import builtins
>>> dir(builtins)
```

## <a name="6_4"></a> 6.4. Packages

Packages are a way of structuring Python’s module namespace by using “dotted module names”. For example, the module name A.B.C designates a submodule named _B_ in a package named _A_. _(A & B are compulsorily modules, C can be module / function / class)_

The ```__init__.py``` files are required to make Python **treat the directories as containing packages**; this is done to prevent directories with a common name, such as ```string```, from unintentionally hiding valid modules that occur later on the module search path. In the simplest case, ```__init__.py``` can just be an empty file, but it can also execute initialization code for the package or set the ```__all__``` variable.

### <a name="6_4_1"></a> 6.4.1. Importing * From a Package

```from module import *``` could take a long time and importing sub-modules might have unwanted side-effects that should only happen when the sub-module is explicitly imported.

The only solution is for the package author to provide an **explicit index of the package**. The ```import``` statement uses the following convention: if a package’s ```__init__.py``` code defines a list named ```__all__```, it is taken to be the __list of module names that should be imported when from package import *__ is encountered.

For example, the file sound/effects/\_\_init\_\_.py could contain the following code:
``` __all__ = ["echo", "surround", "reverse"] ```

This would mean that ```from sound.effects import *``` would import the three named submodules of the sound package.

### <a name="6_4_2"></a> 6.4.2. Intra-package References

When packages are structured into subpackages, you can use **absolute imports** to refer to **submodules of siblings packages**. For example, if the module _sound.filters.vocoder_ needs to use the _echo_ module in the _sound.effects_ package, it can use ```from sound.effects import echo```.

You can also write **relative imports**, with the ```from module import name``` form of import statement. These imports _use leading dots_ to indicate the current and parent packages involved in the relative import.

```python3
from . import echo
from .. import formats
from ..filters import equalizer
```

Note that **relative imports are based on the name of the current module**. Since the name of the main module is always "\_\_main\_\_", modules intended for use as the main module of a Python application must always use absolute imports.
