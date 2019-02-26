[Go Back](./README.md)

# <a name="9"></a> 9. Classes

Classes provide a means of _bundling data and functionality_ together. Creating a new class creates a new type of object, allowing new instances of that type to be made.

Compared with other programming languages, Python’s class mechanism adds classes with a minimum of new syntax and semantics. It is a mixture of the class mechanisms found in _C++_ and _Modula-3_.

- In _C++_ terminology, normally class members are public, and all member functions are virtual.
- As in _Modula-3_, there are no shorthands for referencing the object’s members from its methods: the method function is declared with an _explicit first argument representing the object_, which is provided implicitly by the call.
- As in _Smalltalk_, classes themselves are objects. This provides semantics for importing and renaming.

Unlike _C++_ and _Modula-3_, **built-in types can be used as base classes for extension by the user**.

## <a name="9_1"></a> 9.1. A Word About Names and Objects

Objects have individuality, and multiple names can be bound to the same object. This is known as _aliasing_ in other languages. This is usually _not appreciated on a first glance at Python_, and _can be safely ignored when dealing with immutable basic types_ (numbers, strings, tuples). However, **aliasing has a possibly surprising effect on the semantics of Python code involving _mutable_ objects** such as lists, dictionaries, and most other types. This is usually used to the benefit of the program, since aliases behave like pointers in some respects.

## <a name="9_2"></a> 9.2. Python Scopes and Namespaces

A **namespace** is a _mapping from names to objects_. Most namespaces are currently implemented as Python _dictionaries_, but that’s normally not noticeable in any way (except for performance), and it may change in the future. Examples of namespaces are: the set of built-in names; the global names in a module; and the local names in a function invocation.

In a sense the set of attributes of an object also form a namespace. The important thing to know about namespaces is that _there is absolutely no relation between names in different namespaces_; for instance, two different modules may both define a function ``maximize`` without confusion — users of the modules must prefix it with the module name.

_Namespaces_ are created at different moments and have different lifetimes. The namespace containing the _built-in_ names is created when the Python interpreter starts up, and is never deleted. The _global_ namespace for a module is created when the module definition is read in; normally, module namespaces also last until the interpreter quits. The statements executed by the top-level invocation of the interpreter, either read from a script file or interactively, are considered part of a module called *\_\_main\_\_*, so they have their own global namespace. (The built-in names actually also live in a module; this is called ``builtins``.)

The _local namespace_ for a function is created when the function is called, and deleted when the function returns or raises an exception that is not handled within the function. Recursive invocations each have their own local namespace.

A **scope** is a textual region of a Python program where a _namespace is directly accessible_. “Directly accessible” here means that an unqualified reference to a name attempts to find the name in the namespace.

Although _scopes are determined statically, they are used dynamically_. At any time during execution, there are at least 3 nested scopes whose namespaces are directly accessible:
- the _innermost scope_, which is searched first, contains the local names
- the scopes of _any enclosing functions_, which are searched starting with the nearest enclosing scope, contains non-local, but also non-global names
- the _next-to-last scope_ contains the current module’s global names
- the _outermost scope_ (searched last) is the namespace containing built-in names

A special quirk of Python is that – if no ``global`` statement is in effect – assignments to names always go into the innermost scope. **Assignments do not copy data — they just bind names to objects**. The same is true for deletions: the statement ``del x`` removes the binding of ``x`` from the namespace referenced by the local scope. In fact, all operations that introduce new names use the local scope: in particular, ``import`` statements and function definitions bind the module or function name in the local scope.

The ``global`` statement can be used to indicate that particular variables live in the global scope and should be rebound there; the ``nonlocal`` statement indicates that particular variables live in an enclosing scope and should be rebound there.

### <a name="9_2_1"></a> 9.2.1. Scopes and Namespaces Example

This is an example demonstrating how to reference the different _scopes_ and _namespaces_, and how ``global`` and ``nonlocal`` affect variable binding:

```python3
def scope_test():
    def do_local():
        spam = "local spam"

    def do_nonlocal():
        nonlocal spam
        spam = "nonlocal spam"

    def do_global():
        global spam
        spam = "global spam"

    spam = "test spam"
    do_local()
    print("After local assignment:", spam)
    do_nonlocal()
    print("After nonlocal assignment:", spam)
    do_global()
    print("After global assignment:", spam)

scope_test()
print("In global scope:", spam)
```

The output of the example code is:

```python3
After local assignment: test spam
After nonlocal assignment: nonlocal spam
After global assignment: nonlocal spam
In global scope: global spam
```

Note how the _local_ assignment (which is default) didn’t change _scope_test’s_ binding of _spam_. The ``nonlocal`` assignment changed _scope_test’s_ binding of _spam_, and the ``global`` assignment changed the module-level binding.

You can also see that there was no previous binding for _spam_ before the ``global`` assignment.

## <a name="9_3"></a> 9.3. A First Look at Classes

### <a name="9_3_1"></a> 9.3.1. Class Definition Syntax

The simplest form of class definition looks like this:

```python3
class ClassName:
    <statement-1>
    .
    .
    .
    <statement-N>
```

In practice, the statements inside a class definition will usually be function definitions, but other statements are allowed, and sometimes useful. The function definitions inside a class normally have a peculiar form of argument list, dictated by the calling conventions for methods.

When a class definition is entered, a new namespace is created, and used as the local scope — thus, all assignments to local variables go into this new namespace. In particular, function definitions bind the name of the new function here.

When a class definition is left normally (via the end), a class object is created. This is basically a _wrapper around the contents of the namespace_ created by the class definition. The original local scope (the one in effect just before the class definition was entered) is reinstated, and the class object is bound here to the class name given in the class definition header (``ClassName`` in the example).

### <a name="9_3_2"></a> 9.3.2. Class Objects

Class objects support two kinds of operations: _attribute references_ and _instantiation_.

**Attribute references** use the standard syntax used for all attribute references in Python: ``obj.name``. Valid attribute names are all the names that were in the class’s namespace when the class object was created. So, if the class definition looked like this:

```python3
class MyClass:
    """A simple example class"""
    i = 12345

    def f(self):
        return 'hello world'
```

then ``MyClass.i`` and ``MyClass.f`` are valid attribute references, returning an integer and a function object, respectively. ``__doc__`` is also a valid attribute, returning the docstring belonging to the class: _"A simple example class"_.

**Class instantiation** uses function notation. Just pretend that the class object is a parameterless function that returns a new instance of the class. For example (assuming the above class):

```python3
x = MyClass()
```

creates a new instance of the class and assigns this object to the local variable x.

The instantiation operation (“calling” a class object) creates an empty object. Many classes like to create objects with instances customized to a specific initial state. Therefore a class may define a special method named ``__init__()``. When a class defines an ``__init__()`` method, class instantiation automatically invokes ``__init__()`` for the newly-created class instance.

```python3
def __init__(self):
    self.data = []
```

### <a name="9_3_3"></a> 9.3.3. Instance Objects

The only operations understood by instance objects are _attribute references_. There are two kinds of valid attribute names
- data attributes
- methods

Data attributes need not be declared; like local variables, they spring into existence when they are first assigned to. For example, if ``x`` is the instance of ``MyClass`` created above, the following piece of code will print the value 16, without leaving a trace:

```python3
x.counter = 1
while x.counter < 10:
   x.counter = x.counter * 2
print(x.counter)
del x.counter
```

### <a name="9_3_4"></a> 9.3.4. Method Objects

You may have noticed that ``x.f()`` was called without an argument above, even though the function definition for ``f()`` specified an argument. The special thing about methods is that the **instance object is passed as the first argument of the function**. In our example, the call ``x.f()`` is exactly equivalent to ``MyClass.f(x)``. In general, calling a method with a list of n arguments is equivalent to calling the corresponding function with an argument list that is created by inserting the method’s instance object before the first argument.

When a non-data attribute of an instance is referenced, the instance’s class is searched. If the name denotes a valid class attribute that is a function object, a method object is created by packing (pointers to) the instance object and the function object just found together in an abstract object: this is the method object. When the method object is called with an argument list, a new argument list is constructed from the instance object and the argument list, and the function object is called with this new argument list.

### <a name="9_3_5"></a> 9.3.5. Class and Instance Variables

Generally speaking, **instance variables** are for _data unique to each instance_ and **class variables** are for _attributes and methods shared by all instances_ of the class.

```python3
class Dog:

    kind = 'canine'         # class variable shared by all instances

    def __init__(self, name):
        self.name = name    # instance variable unique to each instance

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.kind                  # shared by all dogs
'canine'
>>> e.kind                  # shared by all dogs
'canine'
>>> d.name                  # unique to d
'Fido'
>>> e.name                  # unique to e
'Buddy'
```

Shared data can have possibly surprising effects with involving mutable objects such as lists and dictionaries. For example, the ``tricks`` list in the following code should not be used as a class variable because just a single list would be shared by all ``Dog`` instances:

```python3
class Dog:

    tricks = []             # mistaken use of a class variable

    def __init__(self, name):
        self.name = name

    def add_trick(self, trick):
        self.tricks.append(trick)

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks                # unexpectedly shared by all dogs
['roll over', 'play dead']
```

Correct design of the class should use an instance variable instead:

```python3
class Dog:

    def __init__(self, name):
        self.name = name
        self.tricks = []    # creates a new empty list for each dog

    def add_trick(self, trick):
        self.tricks.append(trick)

>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks
['roll over']
>>> e.tricks
['play dead']
```

## <a name="9_4"></a> 9.4 Random Remarks

**Data attributes override method attributes with the same name**; to avoid accidental name conflicts, it is wise to use some kind of convention that minimizes the chance of conflicts. Possible conventions include capitalizing method names, prefixing data attribute names with a small unique string (perhaps just an underscore).

Data attributes may be referenced by methods as well as by ordinary users _("Clients")_ of an object. In other words, _classes are not usable to implement pure abstract data types_. In fact, nothing in Python makes it possible to enforce data hiding — it is all based upon convention.

Note that **clients may add data attributes of their own** to an instance object without affecting the validity of the methods, as long as name conflicts are avoided

Often, the first argument of a method is called ``self``. This is nothing more than a convention: the name _self_ has absolutely no special meaning to Python.

**Each value is an object**, and therefore has a class. It is stored as ```object.__class__```.

## <a name="9_5"></a> 9.5. Inheritance

```python3
class DerivedClassName(BaseClassName):
    <statements>
```

_Method references are resolved_ as follows: the corresponding class attribute is searched, descending down the chain of base classes if necessary, and the method reference is valid if this yields a function object.

Derived classes may override methods of their base classes. Because _methods have no special privileges when calling other methods of the same object_, a method of a base class that calls another method defined in the same base class may end up calling a method of a derived class that overrides it. (For C++ programmers: all methods in Python are effectively ``virtual``.)

An overriding method in a derived class may in fact want to extend rather than simply replace the base class method of the same name. There is a simple way to call the base class method directly: just call ``BaseClassName.methodname(self, arguments)``.

Python has two built-in functions that work with inheritance:
- Use ``isinstance()`` to check an instance’s type: ``isinstance(obj, int)`` will be **True** only if ``obj.__class__`` is ``int`` or some _class derived from_ ``int``.
- Use ``issubclass()`` to check class inheritance: ``issubclass(bool, int)`` is ``True`` since ``bool`` is a subclass of ``int``. However, ``issubclass(float, int)`` is False since ``float`` is not a subclass of ``int``.

### <a name="9_5_1"></a> 9.5.1. Multiple Inheritance

```python3
class DerivedClassName(Base1, Base2, Base3):
    <statements>
```

For most purposes, in the simplest cases, you can think of the _search for attributes inherited from a parent class as depth-first, left-to-right_, _not searching twice in the same class_ where there is an overlap in the hierarchy. Thus, if an attribute is not found in ``DerivedClassName``, it is searched for in ``Base1``, then (recursively) in the base classes of ``Base1``, and if it was not found there, it was searched for in ``Base2``, and so on.

In fact, it is slightly more complex than that; **the method resolution order changes dynamically** to support cooperative calls to ``super()``.

Dynamic ordering is necessary because all cases of multiple inheritance exhibit one or more _diamond relationships_ (where at least one of the parent classes can be accessed through multiple paths from the bottommost class). For example, **all classes inherit from** ``object``, so any case of multiple inheritance provides more than one path to reach ``object``. To keep the base classes from being accessed more than once, the _dynamic algorithm linearizes the search order in a way that preserves the left-to-right ordering_ specified in each class, that calls each parent only once, and that is monotonic (meaning that a class can be subclassed without affecting the precedence order of its parents). Taken together, these properties make it possible to design reliable and extensible classes with multiple inheritance.

## <a name="9_6"></a> 9.6. Private Variables

**“Private” instance variables** that cannot be accessed except from inside an object **don’t exist in Python**. However, there is a convention that is followed by most Python code: a name prefixed with an underscore (e.g. ``_spam``) should be treated as a non-public part of the API. _It should be considered an implementation detail and subject to change without notice._

Since there is a valid use-case for class-private members (namely to avoid name clashes of names with names defined by subclasses), there is limited support for such a mechanism, called **name mangling**. Any identifier of the form ``__spam`` (at least two leading underscores, at most one trailing underscore) is textually replaced with ``_classname__spam``, where ``classname`` is the current class name.

Note that the mangling rules are designed mostly to avoid accidents; it still is possible to access or modify a variable that is considered private. This can even be useful in special circumstances, such as in the debugger.

## <a name="9_7"></a> 9.7. Odds and Ends

Sometimes it is useful to have a data type similar to the C “struct”, bundling together a few named data items. An empty class definition will do nicely:

```python3
class Employee:
    pass

john = Employee()  # Create an empty employee record

# Fill the fields of the record
john.name = 'John Doe'
john.dept = 'computer lab'
john.salary = 1000
```

A piece of Python code that expects a particular abstract data type can often be passed a class that emulates the methods of that data type instead. For instance, if you have a function that formats some data from a file object, you can define a class with methods read() and readline() that get the data from a string buffer instead, and pass it as an argument.

Instance method objects have attributes, too: ``m.__self__`` is the instance object with the method ``m()``, and ``m.__func__`` is the function object corresponding to the method.
