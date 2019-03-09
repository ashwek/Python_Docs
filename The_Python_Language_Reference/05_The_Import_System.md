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

## <a name="5_4"></a> 5.4. Loading

If and when a module spec is found, the import machinery will use it (and the loader it contains) when loading the module. Note the following details:
- If there is an existing module object with the given name in ``sys.modules``, import will have already returned it.
- The module will exist in ``sys.modules`` before the loader executes the module code. This is crucial because the module code may (directly or indirectly) import itself; adding it to ``sys.modules`` beforehand prevents unbounded recursion in the worst case and multiple loading in the best.
- If loading fails, the failing module – and only the failing module – gets removed from ``sys.modules``. Any module already in the ``sys.modules`` cache, and any module that was successfully loaded as a side-effect, must remain in the cache. This contrasts with reloading where even the failing module is left in ``sys.modules``.
- After the module is created but before execution, the import machinery sets the import-related module attributes, as summarized in a later section.
- Module execution is the key moment of loading in which the module’s namespace gets populated. Execution is entirely delegated to the loader, which gets to decide what gets populated and how.

### <a name="5_4_1"></a> 5.4.1. Loaders

Module loaders provide the critical function of loading: _module execution_. The import machinery calls the ``importlib.abc.Loader.exec_module()`` method with a single argument, _the module object to execute_. Any value returned from ``exec_module()`` is ignored.

Loaders must satisfy the following requirements:
- If the module is a Python module (as opposed to a built-in module or a dynamically loaded extension), the loader should execute the module’s code in the module’s global name space (``module.__dict__``).
- If the loader cannot execute the module, it should raise an ``ImportError``, although any other exception raised during ``exec_module()`` will be propagated.

Module loaders may opt in to creating the module object during loading by implementing a ``create_module()`` method. It takes one argument, the module spec, and returns the new module object to use during loading. ``create_module()`` does not need to set any attributes on the module object. If the method returns ``None``, the import machinery will create the new module itself.

_Changed in version 3.4:_ The ``load_module()`` method was replaced by ``exec_module()`` and the import machinery assumed all the boilerplate responsibilities of loading.

### <a name="5_4_2"></a> 5.4.2. Submodules

When a submodule is loaded using any mechanism a binding is placed in the parent module’s namespace to the submodule object. For example, if package ``spam`` has a submodule ``foo``, after importing ``spam.foo``, ``spam`` will have an attribute ``foo`` which is bound to the submodule. Let’s say you have the following directory structure:

```
spam/
    __init__.py
    foo.py
    bar.py
```

and ``spam/__init__.py`` has the following lines in it:

```python3
from .foo import Foo
from .bar import Bar
```

### <a name="5_4_3"></a> 5.4.3. Module spec

The import machinery uses a variety of information about each module during import, especially before loading. Most of the information is common to all modules. The purpose of a module’s spec is to encapsulate this import-related information on a per-module basis.

Using a spec during import allows state to be transferred between import system components, e.g. between the finder that creates the module spec and the loader that executes it. Most importantly, it allows the import machinery to perform the boilerplate operations of loading, whereas without a module spec the loader had that responsibility.

The module’s spec is exposed as the ``__spec__`` attribute on a module object.

```python3
>>> import os
>>> os.__spec__
ModuleSpec(name='os', loader=<_frozen_importlib_external.SourceFileLoader object at 0x7fe56df1a438>, origin='/usr/lib/python3.6/os.py')
```

### <a name="5_4_4"></a> 5.4.4. Import-related module attributes

The import machinery fills in these attributes on each module object during loading, based on the module’s spec, before the loader executes the module.

##### \_\_name\_\_
The ``__name__`` attribute must be set to the fully-qualified name of the module. This name is used to uniquely identify the module in the import system.

##### \_\_loader\_\_
The ``__loader__`` attribute must be set to the loader object that the import machinery used when loading the module. This is mostly for introspection, but can be used for additional loader-specific functionality, for example getting data associated with a loader.

##### \_\_package\_\_
The module’s ``__package__`` attribute must be set. Its value must be a string, but it can be the same value as its ``__name__``. When the module is a package, its ``__package__`` value should be set to its ``__name__``. When the module is not a package, ``__package__`` should be set to the empty string for top-level modules, or for submodules, to the parent package’s name.

##### \_\_spec\_\_
The ``__spec__`` attribute must be set to the module spec that was used when importing the module. Setting ``__spec__`` appropriately applies equally to modules initialized during interpreter startup. The one exception is ``__main__``, where ``__spec__`` is set to None in some cases. When ``__package__`` is not defined, ``__spec__.parent`` is used as a fallback.

##### \_\_path\_\_
If the module is a package (either regular or namespace), the module object’s ``__path__`` attribute must be set. The value must be iterable, but may be empty if ``__path__`` has no further significance. If ``__path__`` is not empty, it must produce strings when iterated over.

##### \_\_file\_\_ , \_\_cached\_\_

``__file__`` is optional. If set, this attribute’s value must be a string. The import system may opt to leave ``__file__`` unset if it has no semantic meaning (e.g. a module loaded from a database).
If ``__file__`` is set, it may also be appropriate to set the ``__cached__`` attribute which is the path to any compiled version of the code (e.g. byte-compiled file). The file does not need to exist to set this attribute; the path can simply point to where the compiled file would exist.

It is also appropriate to set ``__cached__`` when ``__file__`` is not set. However, that scenario is quite atypical. Ultimately, the loader is what makes use of ``__file__`` and/or ``__cached__``. So if a loader can load from a cached module but otherwise does not load from a file, that atypical scenario may be appropriate.

### <a name="5_4_5"></a> 5.4.5. module.\_\_path\_\_

By definition, if a module has a ``__path__`` attribute, it is a package.

A package’s ``__path__`` attribute is used during imports of its subpackages. Within the import machinery, it functions much the same as ``sys.path``, i.e. providing a list of locations to search for modules during import. However, ``__path__`` is typically much more constrained than ``sys.path``.

``__path__`` must be an iterable of strings, but it may be empty. The same rules used for ``sys.path`` also apply to a package’s ``__path__``, and ``sys.path_hooks`` are consulted when traversing a package’s ``__path__``.

A package’s ``__init__.py`` file may set or alter the package’s ``__path__`` attribute, and this was typically the way namespace packages were implemented prior to PEP 420. With the adoption of PEP 420, namespace packages no longer need to supply ``__init__.py`` files containing only ``__path__`` manipulation code; the import machinery automatically sets ``__path__`` correctly for the namespace package.

### <a name="5_4_6"></a> 5.4.6. Module reprs

By default, all modules have a usable repr, however depending on the attributes set above, and in the module’s spec, you can more explicitly control the repr of module objects.

If the module has a spec (``__spec__``), the import machinery will try to generate a repr from it. If that fails or there is no spec, the import system will craft a default repr using whatever information is available on the module. It will try to use the ``module.__name__``, ``module.__file__``, and ``module.__loader__`` as input into the repr, with defaults for whatever information is missing.

Here are the exact rules used:

- If the module has a ``__spec__`` attribute, the information in the spec is used to generate the repr. The “name”, “loader”, “origin”, and “has_location” attributes are consulted.
- If the module has a ``__file__`` attribute, this is used as part of the module’s repr.
- If the module has no ``__file__`` but does have a ``__loader__`` that is not ``None``, then the loader’s repr is used as part of the module’s repr.
- Otherwise, just use the module’s ``__name__`` in the repr.

### <a name="5_4_7"></a> 5.4.7. Cached bytecode invalidation

Before Python loads cached bytecode from .pyc file, it checks whether the cache is up-to-date with the source .py file. By default, Python does this _by storing the source’s last-modified timestamp and size in the cache file when writing it_. At runtime, the import system then validates the cache file by checking the stored metadata in the cache file against at source’s metadata.

Python also supports “hash-based” cache files, which store a hash of the source file’s contents rather than its metadata. There are two variants of hash-based .pyc files: _**checked**_ and **_unchecked_**. For checked hash-based .pyc files, Python validates the cache file by hashing the source file and comparing the resulting hash with the hash in the cache file. If a checked hash-based cache file is found to be invalid, Python regenerates it and writes a new checked hash-based cache file. For unchecked hash-based .pyc files, Python simply assumes the cache file is valid if it exists. Hash-based .pyc files validation behavior may be overridden with the ``--check-hash-based-pycs`` flag.

## <a name="5_5"></a> 5.5. The Path Based Finder

As mentioned previously, Python comes with several default meta path finders. One of these, called the path based finder (``PathFinder``), searches an import path, which contains a list of path entries. Each path entry names a location to search for modules.

The path based finder itself doesn’t know how to import anything. Instead, it traverses the individual path entries, associating each of them with a path entry finder that knows how to handle that particular kind of path.

Path entries need not be limited to file system locations. They can refer to URLs, database queries, or any other location that can be specified as a string.

The path based finder provides additional hooks and protocols so that you can extend and customize the types of searchable path entries. For example, if you wanted to support path entries as network URLs, you could write a hook that implements HTTP semantics to find modules on the web. This hook (a callable) would return a path entry finder supporting the protocol described below, which was then used to get a loader for the module from the web.

A word of warning: this section and the previous both use the term finder, distinguishing between them by using the terms **meta path finder and path entry finder**. These two types of finders are very similar, support similar protocols, and function in similar ways during the import process, but it’s important to keep in mind that **they are subtly different**. In particular, meta path finders operate at the beginning of the import process, as keyed off the ``sys.meta_path`` traversal.

By contrast, path entry finders are in a sense an implementation detail of the path based finder, and in fact, if the path based finder were to be removed from ``sys.meta_path``, none of the path entry finder semantics would be invoked.
