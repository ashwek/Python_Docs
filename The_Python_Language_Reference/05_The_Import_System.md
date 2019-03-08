[Go Back](./README.md)

# <a name="5"></a> 5. The Import System

Python code in one module gains access to the code in another module by the process of importing it. it. The ``import`` statement is the most common way of invoking the import machinery, but it is not the only way. Functions such as ``importlib.import_module()`` and built-in ``__import__()`` can also be used to invoke the import machinery.

The ``import`` statement combines two operations; it searches for the named module, then it binds the results of that search to a name in the local scope. The search operation of the import statement is defined as a call to the ``__import__()`` function, with the appropriate arguments. The return value of ``__import__()`` is used to perform the name binding operation of the ``import`` statement.

When a module is first imported, Python searches for the module and if found, it creates a _module object_, initializing it. If the named module cannot be found, a ``ModuleNotFoundError`` is raised. Python implements various strategies to search for the named module when the import machinery is invoked. These strategies can be modified and extended by using various hooks described in the sections below.

## <a name="5_1"></a> 5.1 importlib

The ``importlib`` module provides a rich API for interacting with the import system. For example ``importlib.import_module()`` provides a recommended, simpler API than built-in ``__import__()`` for invoking the import machinery.

## <a name="5_2"></a> 5.2. Packages

Python has _only one type of module object_, and all modules are of this type, regardless of whether the module is implemented in Python, C, or something else. To help organize modules and provide a naming hierarchy, Python has a concept of ``packages``.

You can think of packages as the directories on a file system and modules as files within directories, _but don’t take this analogy too literally since packages and modules need not originate from the file system_. Like file system directories, packages are organized hierarchically, and packages may themselves contain subpackages, as well as regular modules.

It’s important to keep in mind that **all packages are modules, but not all modules are packages**. Or put another way, packages are just a special kind of module. Specifically, any module that contains a ``__path__`` attribute is considered a package.

All modules have a name. Subpackage names are separated from their parent package name by dots, akin to Python’s standard attribute access syntax.

### <a name="5_2_1"></a> 5.2.1. Regular packages

Python defines two types of packages, **regular packages** and **namespace packages**. A regular package is typically implemented as a directory containing an ``__init__.py`` file. When a regular package is imported, this ``__init__.py`` file is **_implicitly executed_**, and the objects it defines are bound to names in the package’s namespace. The ``__init__.py`` file can contain the same Python code that any other module can contain, and _**Python will add** some additional attributes to the module when it is imported_.

For example, the following file system layout defines a top level parent package with three subpackages:

```
parent/
    __init__.py
    one/
        __init__.py
    two/
        __init__.py
    three/
        __init__.py
```

Importing ``parent.one`` will implicitly execute ``parent/__init__.py`` and ``parent/one/__init__.py``.

### <a name="5_2_2"></a> 5.2.2. Namespace packages

A namespace package is a composite of various **portions** _(a set of files in a single directory (possibly stored in a zip file) that contribute to a namespace package)_, where each portion contributes a subpackage to the parent package. Portions may reside in different locations on the file system. Portions may also be found in zip files, on the network, or anywhere else that Python searches during import. Namespace packages may or may not correspond directly to objects on the file system; they may be virtual modules that have no concrete representation.

Namespace packages do not use an ordinary list for their ``__path__`` attribute. They instead use a custom iterable type which will automatically perform a new search for package portions on the next import attempt within that package if the path of their parent package changes.

With namespace packages, there is no ``parent/__init__.py`` file. In fact, _there may be multiple parent directories_ found during import search, where each one is provided by a different portion. Thus ``parent/one`` may not be physically located next to ``parent/two``. In this case, Python will create a namespace package for the top-level parent package whenever it or one of its subpackages is imported.

## <a name="5_3"></a> 5.3. Searching

To begin the search, Python needs the fully qualified name of the module being imported. This name may come from various arguments to the ``import`` statement, or from the parameters to the ``importlib.import_module()`` or ``__import__()`` functions.

This name will be used in various phases of the import search, and it may be the dotted path to a submodule, e.g. ``foo.bar.baz``. In this case, Python first tries to import ``foo``, then ``foo.bar``, and finally ``foo.bar.baz``. If any of the intermediate imports fail, a ``ModuleNotFoundError`` is raised.

### <a name="5_3_1"></a> 5.3.1. The module cache

The _first place checked_ during import search is ``sys.modules``. This mapping _serves as a cache of all modules that have been previously imported_, including the intermediate paths. So if ``foo.bar.baz`` was previously imported, ``sys.modules`` will contain entries for ``foo``, ``foo.bar``, and ``foo.bar.baz``. Each key will have as its value the corresponding module object.

During import, the module name is looked up in ``sys.modules`` and if present, _the associated value (module object) is the module satisfying the import_, and the process completes. However, if the value is ``None``, then a ``ModuleNotFoundError`` is raised. If the module name is missing, Python will continue searching for the module.

### <a name="5_3_2"></a> 5.3.2. Finders and loaders

If the named module is not found in ``sys.modules``, then Python’s import protocol is invoked to find and load the module. This protocol consists of _two conceptual objects_, **finders** and **loaders**. A finder’s job is to determine whether it can find the named module using whatever strategy it knows about. Objects that implement both of these interfaces are referred to as **importers** - they return themselves when they find that they can load the requested module.

Python includes a number of default finders and importers to locate built-in modules, to locate frozen modules, to search an ``import path`` for modules. The ``import path`` is a list of locations that may name file system paths or zip files. It can also be extended to search for any locatable resource, such as those identified by URLs.

Finders do not actually load modules. If they can find the named module, they return a _module spec_, an encapsulation of the module’s import-related information and loaders, which the import machinery then uses when loading the module.

### <a name="5_3_3"></a> 5.3.3. Import hooks

The import machinery is designed to be extensible; the primary mechanism for this are the _import hooks_. There are two types of import hooks:
- meta hooks
- import path hooks.

Meta hooks are called at the start of import processing, before any other import processing has occurred, other than ``sys.modules`` cache look up. This allows meta hooks to override ``sys.path`` processing, frozen modules, or even built-in modules. Meta hooks are registered by adding new finder objects to ``sys.meta_path``.

Import path hooks are called as part of ``sys.path`` (or ``package.__path__``) processing, at the point where their associated path item is encountered. Import path hooks are registered by adding new callables to ``sys.path_hooks``.

### <a name="5_3_4"></a> 5.3.4. The meta path

When the named module is not found in ``sys.modules``, Python next searches ``sys.meta_path``, which contains a list of meta path finder objects. These finders are queried in order to see if they know how to handle the named module. Meta path finders must implement a method called ``find_spec()`` which takes three arguments: _a name_, _an import path_, and (optionally) _a target module_. The meta path finder can use any strategy it wants to determine whether it can handle the named module or not.

If the meta path finder knows how to handle the named module, it returns a spec object. If it cannot handle the named module, it returns ``None``. If ``sys.meta_path`` processing reaches the end of its list without returning a spec, then a ``ModuleNotFoundError`` is raised. Any other exceptions raised are simply propagated up, aborting the import process.

The ``find_spec()`` method of meta path finders is called with two or three arguments. The first is the _fully qualified name_ of the module being imported, for example ``foo.bar.baz``. The second argument is _the path entries to use for the module search_. For top-level modules, the second argument is ``None``, but for submodules or subpackages, the second argument is the value of the parent package’s ``__path__`` attribute. If the appropriate ``__path__`` attribute cannot be accessed, a ``ModuleNotFoundError`` is raised. The third argument is an existing module object that will be the target of loading later. _**The import system passes in a target module only during reload.**_

The meta path may be traversed multiple times for a single import request. For example, assuming none of the modules involved has already been cached, importing ``foo.bar.baz`` will first perform a top level import, calling ``mpf.find_spec("foo", None, None)`` on each meta path finder (mpf). After ``foo`` has been imported, ``foo.bar`` will be imported by traversing the meta path a second time, calling ``mpf.find_spec("foo.bar", foo.__path__, None)``. Once ``foo.bar`` has been imported, the final traversal will call ``mpf.find_spec("foo.bar.baz", foo.bar.__path__, None)``.

Python’s default ``sys.meta_path`` has _three meta path finders_, one that knows how to import _built-in modules_, one that knows how to import _frozen modules_, and one that knows how to import _modules from an import path_.
