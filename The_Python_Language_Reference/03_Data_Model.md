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

- **User-defined functions** A user-defined function object is created by a function definition. Function objects also support getting and setting arbitrary attributes, which can be used, for example, to attach metadata to functions. Regular attribute dot-notation is used to get and set such attributes.
