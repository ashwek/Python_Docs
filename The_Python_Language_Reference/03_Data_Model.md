[Go Back](./README.md)

# <a name="3"></a> 3. Data model

## <a name="3_1"></a> 3.1. Objects, values and types

_Objects_ are Python’s abstraction for data. All data in a Python program is represented by objects or by relations between objects.

Every object has an _identity_, a _type_ and a _value_. An object’s _identity_ never changes once it has been created; you may think of it as the object’s address in memory. The ``is`` operator compares the identity of two objects; the ``id()`` function returns an integer representing its identity.

**CPython** implementation detail: For CPython, ``id(x)`` is the _memory address where x is stored_.

An object’s type determines the operations that the object supports & also the possible values of that type. The ``type()`` function returns an object’s type (which is an object itself). Like its identity, an object’s type is also unchangeable.

The _value_ of some objects can change. Objects whose value can change are said to be **mutable**; objects whose value is unchangeable once they are created are called **immutable**. (The value of an immutable container object that contains a reference to a mutable object can change when the latter’s value is changed; however the container is still considered immutable, because the collection of objects it contains cannot be changed. **_So, immutability is not strictly the same as having an unchangeable value, it is more subtle._**) An object’s mutability is determined by its type; for instance, **numbers, strings and tuples are immutable**, while **dictionaries and lists are mutable**.

**Objects are never explicitly destroyed**; however, when they become unreachable they may be garbage-collected. An implementation is allowed to postpone garbage collection or omit it altogether — it is a matter of implementation quality how garbage collection is implemented, as long as no objects are collected that are still reachable.

Some objects contain references to other objects; these are called **containers**. Examples of containers are _tuples_, _lists_ and _dictionaries_.

## <a name="3_2"></a> 3.2. The standard type hierarchy

**None** This type has a single value. There is a single object with this value. This object is accessed through the built-in name ``None``. It is used to signify the absence of a value in many situations, e.g., it is returned from functions that don’t explicitly return anything. Its truth value is ``false``.

**NotImplemented** This type has a single value. There is a single object with this value. This object is accessed through the built-in name ``NotImplemented``. Numeric methods and rich comparison methods should return this value if they do not implement the operation for the operands provided. Its truth value is ``true``.

**Ellipsis** This type has a single value. There is a single object with this value. This object is accessed through the literal ``...`` or the built-in name ``Ellipsis``. Its truth value is ``true``.

**numbers.Number** Numeric objects are immutable; once created their value never changes. Python numbers are subject to the limitations of numerical representation in computers. Python distinguishes between _integers_, _floating point_ numbers, and _complex_ numbers:

- **numbers.Integral** There are two types of integers:

    - **Integers (int)** These represent numbers in an unlimited range, subject to available (virtual) memory only and negative numbers are represented in a variant of 2’s complement.

    - **Booleans (bool)** These represent the truth values ``False`` and ``True``. The Boolean type is a subtype of the integer type, and Boolean values behave like the values 0 and 1, respectively, in almost all contexts, the exception being that when converted to a string, the strings "False" or "True" are returned, respectively.

- **numbers.Real (float)** You are at the mercy of the underlying machine architecture for the accepted range and handling of overflow. Python does not support single-precision floating point numbers; the savings in processor and memory usage that are usually the reason for using these are dwarfed by the overhead of using objects in Python, so there is no reason to complicate the language with two kinds of floating point numbers.

- **numbers.Complex (complex)** These represent complex numbers as a pair of machine-level double precision floating point numbers. The same caveats apply as for floating point numbers. The real and imaginary parts of a complex number z can be retrieved through the read-only attributes ``z.real`` and ``z.imag``.

**Sequences**
- Immutable sequences
    - **Strings**
    - **Tuples**
    - **Bytes**
- Mutable sequences
    - **Lists**
    - **Byte Arrays**

**Set Types** These represent unordered, finite sets of unique, immutable objects. Common uses for sets are fast membership testing, removing duplicates from a sequence, and computing mathematical operations such as intersection, union, difference, and symmetric difference.

- **Sets** These represent a **_mutable set_**. They are created by the built-in ``set()`` constructor.

- **Frozen sets** These represent an **_immutable set_**. They are created by the built-in ``frozenset()`` constructor. As a frozenset is immutable and hashable, it can be used again as an element of another set, or as a dictionary key.

**Mappings** The subscript notation ``a[k]`` selects the item indexed by ``k`` from the mapping ``a``. The built-in function ``len()`` returns the number of items in a mapping.

- **Dictionaries** The only types of values **_not acceptable as keys_** are values containing _lists_ or _dictionaries_ or _other mutable types_ that are compared by value rather than by object identity, the reason being that the efficient implementation of dictionaries requires a key’s hash value to remain constant. Dictionaries are **_mutable_**.

**Callable types** These are the types to which the function call operation can be applied:

- **User-defined functions** A user-defined **_function object_** is created by a _function definition_. Function objects also support getting and setting arbitrary attributes, which can be used, for example, to attach metadata to functions. Regular attribute dot-notation is used to get and set such attributes.

- **Instance methods** An instance method object combines a class, a class instance and any callable object (normally a user-defined function).

    Special read-only attributes: ``__self__`` is the _class instance object_, ``__func__`` is the _function object_; ``__doc__`` is the method’s _documentation_ (same as ``__func__.__doc__``); ``__name__`` is the method name (same as ``__func__.__name__``); ``__module__`` is the name of the module the method was defined in, or ``None`` if unavailable.

    _User-defined method objects_ may be created when getting an attribute of a class (via an instance of that class), if that attribute is a _user-defined function object_ or a _class method object_.

    When an _instance method object_ is created by retrieving a _user-defined function object_ from a class via one of its instances, its ``__self__`` attribute is the instance, and the **_method object_ is said to be bound**. The new method’s ``__func__`` attribute is the _original function object_.

    When an _instance method object_ is called, the underlying function (``__func__``) is called, inserting the class instance (``__self__``) in front of the argument list. For instance, when ``C`` is a class which contains a definition for a function ``f()``, and ``x`` is an instance of ``C``, calling ``x.f(1)`` is equivalent to calling ``C.f(x, 1)``.

```python3
>>> class C:
...     def hello(self):
...             self.c = 10
...             print(c)
...     def abc():
...             print("abc")
...
>>> abc = C()   # abc is class instance
>>> C.abc   # class method object
<function C.abc at 0x7f0b7c5d3158>
>>> abc.hello   # bound instance method object
<bound method C.hello of <__main__.C object at 0x7f0b7c5c6e10>>
>>> abc.hello.__func__   # orignal function object
<function C.hello at 0x7f0b7c5d30d0>
>>>
```

- **Generator functions**
    A function or method which uses the ``yield`` statement is called a _generator function_. Such a function, when called, _always returns an iterator object_ which can be used to execute the body of the function: calling the iterator’s ``iterator.__next__()`` method will cause the function to execute until it provides a value using the ``yield`` statement. When the function executes a ``return`` statement or falls off the end, a ``StopIteration`` exception is raised and the iterator will have reached the end of the set of values to be returned.

- **Coroutine functions**
    A function or method which is defined using ``async def`` is called a _coroutine function_. Such a function, when called, _returns a **coroutine** object_. Coroutines is a more generalized form of subroutines. Subroutines are entered at one point and exited at another point. Coroutines can be entered, exited, and resumed at many different points. They can be implemented with the async def statement.

- **Asynchronous generator functions**
    A function or method which is defined using ``async def`` and which uses the ``yield`` statement is called a asynchronous generator function. Such a function, when called, returns an _asynchronous iterator object_ which can be used in an ``async for`` statement to execute the body of the function.

- **Built-in functions**
    A built-in function object is a _wrapper_ around a C function. Examples of built-in functions are ``len()`` and ``math.sin()`` (``math`` is a standard built-in module). The number and type of the arguments are determined by the C function.

- **Built-in methods**
    This is really a different disguise of a built-in function, this time containing _an object passed to the C function_ as an implicit extra argument. An example of a built-in method is ``alist.append()``, assuming ``alist`` is a ``list`` object. In this case, the special read-only attribute ``__self__`` is set to the object denoted by ``alist``.

- **Classes**
    Classes are callable. These objects normally act as factories for new instances of themselves, but variations are possible for class types that override ``__new__()``. The arguments of the call are passed to ``__new__()`` and, in the typical case, to ``__init__()`` to initialize the new instance.

- **Class Instances**
    Instances of arbitrary classes can be made callable by defining a ``__call__()`` method in their class.

**Modules**
    Modules are a basic organizational unit of Python code, and are created by the ``import`` system. A module object has a namespace implemented by a dictionary object (this is the dictionary referenced by the ``__globals__`` attribute of functions defined in the module).

**Custom classes**
    Custom class types are typically _created by class definitions_. A class has a _namespace implemented by a dictionary object_. Class attribute references are translated to lookups in this dictionary, e.g., ``C.x`` is translated to ``C.__dict__["x"]``. Class attribute assignments update the class’s dictionary, never the dictionary of a base class.

**Class instances**
    A class instance is created by calling a class object. A class instance has a namespace implemented as a dictionary which is the first place in which attribute references are searched. When an attribute is not found there, and the instance’s class has an attribute by that name, the search continues with the class attributes. If a class attribute is found that is a user-defined function object, it is transformed into an instance method object whose ``__self__`` attribute is the instance. Static method and class method objects are also transformed. If no class attribute is found, and the object’s class has a ``__getattr__()`` method, that is called to satisfy the lookup. Attribute assignments and deletions update the instance’s dictionary, never a class’s dictionary. If the class has a ``__setattr__()`` or ``__delattr__()`` method, this is called instead of updating the instance dictionary directly.

**I/O objects (also known as file objects)**
    A file object represents an open file.  The objects ``sys.stdin``, ``sys.stdout`` and ``sys.stderr`` are initialized to file objects corresponding to the interpreter’s _standard input, output and error streams_; they are all _open in text mode_.

**Internal types**
A few types used internally by the interpreter are exposed to the user. Their definitions may change with future versions of the interpreter, but they are mentioned here for completeness.

- **Code objects**
    Code objects represent byte-compiled executable Python code, or **_bytecode_**. The difference between a code object and a function object is that the function object contains an explicit reference to the function’s globals (the module in which it was defined), while a code object contains no context; also the default argument values are stored in the function object, not in the code object.

- **Frame objects**
    Frame objects represent execution frames. They may occur in _traceback objects_, and are also passed to registered trace functions.

- **Traceback objects**
    Traceback objects represent a stack trace of an exception. A traceback object is implicitly created when an exception occurs, and may also be explicitly created by calling ``types.TracebackType``. For implicitly created tracebacks, when the search for an exception handler unwinds the execution stack, at each unwound level a traceback object is inserted in front of the current traceback. When an exception handler is entered, the stack trace is made available to the program. It is accessible as the third item of the tuple returned by ``sys.exc_info()``, and as the ``__traceback__`` attribute of the caught exception.

- **Slice objects**
    Slice objects are used to represent slices for ``__getitem__()`` methods. They are also created by the built-in ``slice()`` function.

- **Static method objects**
    Static method objects provide a way of defeating the transformation of function objects to method objects described above. A static method object is a wrapper around any other object, usually a user-defined method object. When a static method object is retrieved from a class or a class instance, the object actually returned is the wrapped object, which is not subject to any further transformation. Static method objects are not themselves callable, although the objects they wrap usually are. Static method objects are created by the built-in ``staticmethod()`` constructor.

- **Class method objects**
    A class method object, like a static method object, is a wrapper around another object that alters the way in which that object is retrieved from classes and class instances. The behaviour of class method objects upon such retrieval is described above, under “User-defined methods”. Class method objects are created by the built-in ``classmethod()`` constructor.


## <a name="3_3"></a> 3.3. Special method names

A class can implement certain operations that are invoked by special syntax by defining methods with special names. This is Python’s approach to _operator overloading_, allowing classes to define their own behavior with respect to operators. For instance, if a class defines a method named ``__getitem__()``, and ``x`` is an instance of this class, then ``x[i]`` is roughly equivalent to ``type(x).__getitem__(x, i)``.

Setting a special method to ``None`` indicates that the corresponding operation is not available. For example, if a class sets ``__iter__()`` to ``None``, the class is not iterable, so calling ``iter()`` on its instances will raise a ``TypeError``.

```python3
>>> class a:
...     __iter__ = None
...
>>> iter(a)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'type' object is not iterable
```

### <a name="3_3_1"></a> 3.3.1. Basic customization

##### object.\_\_new\_\_(cls[, ...])

Called to create a new instance of class ``cls. __new__()`` is a static method that takes the class of which an instance was requested as its first argument. The remaining arguments are those passed to the object constructor expression (the call to the class). The return value of ``__new__()`` should be the new object instance (usually an instance of ``cls``).

Typical implementations create a new instance of the class by invoking the superclass’s ``__new__()`` method using ``super().__new__(cls[, ...])`` with appropriate arguments and then modifying the newly-created instance as necessary before returning it.

If ``__new__()`` returns an instance of ``cls``, then the new instance’s ``__init__()`` method will be invoked like ``__init__(self[, ...])``, where ``self`` is the new instance and the remaining arguments are the same as were passed to ``__new__()``.

**If ``__new__()`` does not return an instance of ``cls``, then the new instance’s ``__init__()`` method will not be invoked.**

``__new__()`` is intended mainly to _allow subclasses of immutable types to customize instance creation_.


##### object.\_\_init\_\_(self[, ...])

Called after the instance has been created (by ``__new__()``), but before it is returned to the caller. The arguments are those passed to the class constructor expression. If a base class has an ``__init__()`` method, the derived class’s ``__init__()`` method, if any, must explicitly call it to ensure proper initialization of the base class part of the instance; for example: ``super().__init__([args...])``.

Because ``__new__()`` and ``__init__()`` work together in constructing objects (``__new__()`` to create it, and ``__init__()`` to customize it), no non-None value may be returned by ``__init__()``; doing so will cause a ``TypeError`` to be raised at runtime.

##### object.\_\_del\_\_(self)

Called when the instance is about to be destroyed. This is also called a finalizer or (improperly) a destructor. If a base class has a ``__del__()`` method, the derived class’s ``__del__()`` method, if any, must explicitly call it to ensure proper deletion of the base class part of the instance.

``del x`` doesn’t directly call ``x.__del__()`` — the former decrements the reference count for ``x`` by one, and the latter is only called when x’s reference count reaches zero.

##### object.\_\_repr\_\_(self)

Called by the ``repr()`` built-in function to compute the “official” string representation of an object. If at all possible, this should look like a valid Python expression that could be used to recreate an object with the same value. If this is not possible, a string of the form ``<...some useful description...>`` should be returned. **The return value must be a string object**. If a class defines ``__repr__()`` but not ``__str__()``, then ``__repr__()`` is also used when an “informal” string representation of instances of that class is required.

##### object.\_\_str\_\_(self)

Called by ``str(object)`` and the built-in functions ``format()`` and ``print()`` to compute the “informal” or nicely printable string representation of an object. **The return value must be a string object**.

This method differs from ``object.__repr__()`` in that there is no expectation that ``__str__()`` return a valid Python expression: a more convenient or concise representation can be used.

The default implementation defined by the built-in type object calls ``object.__repr__()``.

##### object.\_\_bytes\_\_(self)

Called by ``bytes`` to compute a byte-string representation of an object. _This should return a bytes object_.

##### object.\_\_format\_\_(self, format_spec)

Called by the ``format()`` built-in function, and by extension, evaluation of formatted string literals and the ``str.format()`` method, to produce a “formatted” string representation of an object. The return value must be a string object.

##### object.\_\_lt\_\_(self, other)
##### object.\_\_le\_\_(self, other)
##### object.\_\_eq\_\_(self, other)
##### object.\_\_ne\_\_(self, other)
##### object.\_\_gt\_\_(self, other)
##### object.\_\_ge\_\_(self, other)

These are the so-called “rich comparison” methods. The correspondence between operator symbols and method names.
A rich comparison method may return the singleton ``NotImplemented`` if it does not implement the operation for a given pair of arguments. By convention, ``False`` and ``True`` are returned for a successful comparison. However, these methods can return any value, so if the comparison operator is used in a Boolean context, Python will call ``bool()`` on the value to determine if the result is true or false.

##### object.\_\_hash\_\_(self)

Called by built-in function ``hash()`` and for operations on members of hashed collections including ``set``, ``frozenset``, and ``dict. __hash__()`` should return an integer. The only required property is that objects which compare equal have the same hash value; it is advised to mix together the hash values of the components of the object that also play a part in comparison of objects by packing them into a tuple and hashing the tuple.

##### object.\_\_bool\_\_(self)

Called to implement truth value testing and the built-in operation ``bool()``; should return ``False`` or ``True``. When this method is not defined, ``__len__()`` is called, if it is defined, and the object is considered true if its result is nonzero. If a class defines neither ``__len__()`` nor ``__bool__()``, all its instances are considered true.

### <a name="3_3_2"></a> 3.3.2. Customizing attribute access
