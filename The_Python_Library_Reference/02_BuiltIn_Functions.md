[Go Back](./README.md)

# <a name="2"></a> 2. Built-In Functions

<table>
    <tr>
        <th colspan="3">Functions</th>
    </tr>
    <tr>
        <td><a href="#abs">abs()</a></td>
        <td><a href="#all">all()</a></td>
        <td><a href="#any">any()</a></td>
    </tr>
    <tr>
        <td><a href="#bin">bin()</a></td>
        <td><a href="#bool">bool()</a></td>
        <td><a href="#breakpoint">breakpoint()</a></td>
    </tr>
    <tr>
        <td><a href="#bytearray">bytearray()</a></td>
        <td><a href="#bytes">bytes()</a></td>
        <td><a href="#callable">callable()</a></td>
    </tr>
    <tr>
        <td><a href="#chr">chr()</a></td>
        <td><a href="#classmethod">@classmethod</a></td>
        <td><a href="#compile">compile()</a></td>
    </tr>
    <tr>
        <td><a href="#complex">complex()</a></td>
        <td><a href="#delattr">delattr</a></td>
        <td><a href="#dict">dict()</a></td>
    </tr>
    <tr>
        <td><a href="#dir">dir()</a></td>
        <td><a href="#divmod">divmod()</a></td>
        <td><a href="#enumerate">enumerate()</a></td>
    </tr>
    <tr>
        <td><a href="#eval">eval()</a></td>
        <td><a href="#exec">exec()</a></td>
        <td><a href="#filter">filter()</a></td>
    </tr>
    <tr>
        <td><a href="#float">float()</a></td>
        <td><a href="#format">format()</a></td>
        <td><a href="#frozenset">frozenset()</a></td>
    </tr>
    <tr>
        <td><a href="#getattr">getattr()</a></td>
        <td><a href="#globals">globals()</a></td>
        <td><a href="#hasattr">hasattr()</a></td>
    </tr>
    <tr>
        <td><a href="#hash">hash()</a></td>
        <td><a href="#help">help()</a></td>
        <td><a href="#hex">hex()</a></td>
    </tr>
    <tr>
        <td><a href="#id">id()</a></td>
        <td><a href="#input">input()</a></td>
        <td><a href="#int">int()</a></td>
    </tr>
    <tr>
        <td><a href="#isinstance">isinstance()</a></td>
        <td><a href="#issubclass">issubclass()</a></td>
        <td><a href="#iter">iter()</a></td>
    </tr>
    <tr>
        <td><a href="#len">len()</a></td>
        <td><a href="#list">list()</a></td>
        <td><a href="#locals">locals()</a></td>
    </tr>
    <tr>
        <td><a href="#map">map()</a></td>
        <td><a href="#max">max()</a></td>
        <td><a href="#min">min()</a></td>
    </tr>
    <tr>
        <td><a href="#next">next()</a></td>
    </tr>
</table>

----

### <a name="abs"></a> abs( <i>x</i> )
Return absolute value. Argument may be an integer / floating. If the argument is a complex number, its magnitude is returned.

### <a name="all"></a> all( <i>iterable</i> )
Return ``True`` if all elements in the iterable are ``True`` or if the iterator is empty.

### <a name="any"></a> any( <i>iterable</i> )
Return ``True`` if any element in the iterable is ``True``. ``False`` if the iterable is empty.

### <a name="bin"></a> bin( <i>x</i> )
Convert an integer number to a binary string prefixed with “0b”. The result is a valid Python expression. If _x_ is not a Python int object, it has to define an ``__index__()`` method that returns an integer.

### <a name="bool"></a> bool( <i>[x]</i> )
Returns ``True`` or ``False``. If _x_ is false or omitted, this returns ``False``.

### <a name="breakpoint"></a> breakpoint( <i>\*args, \*\*kws</i> )
This function drops you into the debugger at the call site. Specifically, it calls ``sys.breakpointhook()``, passing ``args`` and ``kws`` straight through.

### <a name="bytearray"></a> bytearray( <i>[source[, encoding[, errors]]] </i>)

### <a name="bytes"></a> bytes( <i>[source[, encoding[, errors]]]</i> )

### <a name="callable"></a> callable( <i>object</i> )
Return ``True`` if the object argument appears callable, ``False`` if not. If this returns true, it is still possible that a call fails, but if it is false, calling object will never succeed. Note that classes are callable (calling a class returns a new instance); instances are callable if their class has a ``__call__()`` method.

### <a name="chr"></a> chr( <i>i</i> )
Returns string representation of a character whose _Unicode code point_ is the integer _i_. ``chr(97)`` returns ``'a'``.

### <a name="classmethod"></a> @classmethod
Transform a method into a class method. A class method receives the **class** as implicit first argument, just like an instance method receives the instance. To declare a class method, use this idiom:

```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

The ``@classmethod`` form is a _function decorator_. A class method can be called either on the class or on an instance. Class methods are different than static methods.

### <a name="compile"></a> compile( <i>source, filename, mode, flags=0, dont_inherit=False, optimize=-1</i> )
Compile the source into a code or AST (Abstract Source Tree) object. Code objects can be executed by ``exec()`` or ``eval()``. source can either be a normal string, a byte string, or an AST object.

### <a name="complex"></a> complex( <i>[real[, imag]]</i> )
Return a complex number with the value ``real + imag*1j`` or convert a string or number to a complex number. If the first parameter is a string, it will be interpreted as a complex number and the function must be called without a second parameter. The second parameter can never be a string. Each argument may be any numeric type (including complex). If _imag_ is omitted, it defaults to zero and the constructor serves as a numeric conversion like int and float. If both arguments are omitted, returns ``0j``.

### <a name="delattr"></a> delattr( <i>object, name</i> )
This is a relative of setattr(). The arguments are an object and a string. The string must be the name of one of the object’s attributes. The function deletes the named attribute, provided the object allows it. For example, delattr(x, 'foobar') is equivalent to del x.foobar.

### <a name="dict"></a> dict( )
### dict( <i>\*\*kwarg</i> )
### dict( <i>mapping, \*\*kwarg</i> )
### dict( <i>iterable, \*\*kwarg</i> )
Create a new dictionary. The ``dict`` object is the dictionary class.

```python
>>> dict()    # dict()
{}
>>> dict(one=1, two=2)    # dict(**kwarg)
{'one': 1, 'two': 2}
>>> dict(dict(one=1, two=2), three=3, four=4)    # dict(mapping, **kwarg)
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
>>> dict((("one",1), ("two",2)), three=3, four=4)    # dict(iterable, **kwarg)
{'one': 1, 'two': 2, 'three': 3, 'four': 4}
```

### <a name="dir"></a> dir( <i>[object]</i> )

Without arguments, return the list of names in the current local scope. With an argument, attempt to return a list of valid attributes for that object.

If the object has a method named ``__dir__()``, this method will be called and must return the list of attributes. This allows objects that implement a custom ``__getattr__()`` or ``__getattribute__()`` function to customize the way ``dir()`` reports their attributes.

### <a name="divmod"></a> divmod( <i>a, b</i> )
returns ``(a // b, a % b)``

### <a name="enumerate"></a> enumerate( <i>iterable, start=0</i> )
Return an enumerate object. iterable must be a sequence, an iterator, or some other object which supports iteration.

```python
>>> enumerate(["one", "two", "three"])
<enumerate object at 0x7f8b57123048>
>>> list(enumerate(["one", "two", "three"]))
[(0, 'one'), (1, 'two'), (2, 'three')]
>>> list(enumerate(["one", "two", "three"], start=5))
[(5, 'one'), (6, 'two'), (7, 'three')]
```

### <a name="eval"></a> eval( <i>expression, globals=None, locals=None</i> )
``eval`` accepts **only a single expression**. The arguments are a string and optional globals and locals. If provided, globals must be a dictionary. If provided, locals can be any mapping object.

The expression argument is parsed and evaluated as a Python expression using the globals and locals dictionaries as global and local namespace.

If the globals dictionary is present and does not contain a value for the key ``__builtins__``, a reference to the dictionary of the built-in module ``builtins`` is inserted under that key before expression is parsed. This means that expression normally has full access to the standard builtins module and restricted environments are propagated. If the locals dictionary is omitted it defaults to the globals dictionary. If both dictionaries are omitted, the expression is executed in the environment where ``eval()`` is called. The return value is the result of the evaluated expression. Syntax errors are reported as exceptions.

```python
>>> x = 1
>>> eval('x+1')
2
```

### <a name="exec"></a> exec( <i>object[, globals[, locals]]</i> )
``exec`` can take a code block that has Python statements: ``loops``, ``try: except:``, ``class`` and so on. This function supports dynamic execution of Python code. _object_ must be either a string or a code object. If it is a string, the string is parsed as a suite of Python statements which is then executed. If it is a code object, it is simply executed. In all cases, the code that’s executed is expected to be valid as file input. ``exec`` ignores the return value from its code, and always returns ``None``.


### <a name="filter"></a> filter( <i>function, iterable</i> )

Construct an iterator from those elements of _iterable_ for which function returns **True**. If function returns **None**, it is assumed to be **False**.

```python3
>>> def is_even(num):
...     return num & 1 == 0
>>> list(filter(is_even, range(11)))
[0, 2, 4, 6, 8, 10]
```


### <a name="float"></a> <i>class</i> float( <i>[x]</i> )

Return a floating point number constructed from a _number_ or _string x_. If no argument is given, _0.0_ is returned.

The argument may also be a string representing a **NaN**, or a positive or negative infinity. More precisely, the input must conform to the following grammar after leading and trailing whitespace characters are removed:

```
   sign           ::=  "+" | "-"
   infinity       ::=  "Infinity" | "inf"
   nan            ::=  "nan"
   numeric_value  ::=  floatnumber | infinity | nan
   numeric_string ::=  [sign] numeric_value
```


### <a name="format"></a> format( <i>value[, format_spec] </i> )

Convert a value to a _formatted_ representation, as controlled by *format_spec*. The interpretation of *format_spec* will depend on the type of the value argument.

The default *format_spec* is an empty string which usually gives the same effect as calling ``str(value)``.

```python3
>>> "{} and {}".format("abc", "xyz")
'abc and xyz'
>>> "{1} and {0}".format("abc", "xyz")
'xyz and abc'
>>> "{one} and {two}".format(one = "abc", two = "xyz")
'abc and xyz'
```


### <a name="frozenset"></a> <i>class</i> frozenset( <i>[iterable]</i> )

Return a new **frozenset** object, optionally with elements taken from _iterable_.


### <a name="getattr"></a> getattr( <i>object, name[, default]</i> )

Return the value of the named attribute of _object_ (of user-defined class). _name_ must be a string. If the string is the name of one of the object’s attributes, the result is the value of that attribute.


### <a name="globals"></a> globals()

Return a _dictionary_ representing the current global symbol table. This is always the dictionary of the current module (inside a function or method, this is the module where it is defined, not the module from which it is called).


### <a name="hasattr"></a> hasattr( <i>object, name</i> )

The arguments are an _object_ and a _string_. The result is **True** if the _string_ is the name of one of the _object_’s attributes, **False** if not.


### <a name="hash"></a> hash( <i>object</i> )

Return the hash value of the _object_ (if it has one). Hash values are integers. They are used to quickly compare dictionary keys during a dictionary lookup.


### <a name="help"></a> help( <i>[object]</i> )

Invoke the built-in _help_ system.


### <a name="hex"></a> hex( <i>x</i> )

Convert an integer number to a lowercase hexadecimal string prefixed with _0x_. If _x_ is not a Python _int_ object, it has to define an ``__index__()`` method that returns an integer.

```python3
>>> hex(255)
'0xff'
>>> hex(-42)
'-0x2a'
```


### <a name="id"></a> id( <i>object</i> )

Return the “identity” of an object. This is an _integer_ which is guaranteed to be **unique and constant for this object during its lifetime**. Two objects with non-overlapping lifetimes may have the same ``id()`` value.

**CPython implementation detail:** This is the address of the object in memory.


### <a name="input"></a> input( <i>[prompt]</i> )

If the _prompt_ argument is present, it is written to standard output without a trailing newline. The function then reads a line from input, converts it to a string (stripping a trailing newline), and returns that.


### <a name="int"></a> <i>class</i> int( <i>[x]</i> )
### <i>class</i> int( <i>x, base=10</i> )

Return an integer object constructed from a number or string _x_, or return 0 if no arguments are given.

```python3
>>> int('456')
456
>>> int('456.56')
ValueError: invalid literal for int() with base 10: '456.56'
>>> int('0x15', base=16)
21
>>> int('0o15', base=8)
13
>>> int('0b1101', base=2)
13
```


### <a name="isinstance"></a> isinstance( <i>object, classinfo</i> )

Return **True** if the _object_ argument is an instance of the _classinfo_ argument, or of a (direct, indirect or virtual) subclass thereof. If _classinfo_ is a **tuple of type** objects.


### <a name="issubclass"></a> issubclass( <i>class, classinfo</i> )

Return **True** if _class_ is a subclass (direct, indirect or virtual) of _classinfo_. A class is considered a subclass of itself. _classinfo_ may be a tuple of class objects.


### <a name="iter"></a> iter( <i>object[, sentinel]</i> )

Return an iterator object. The first argument is interpreted very differently depending on the presence of the second argument.

Without a second argument, _object_ must be a collection object which supports the iteration protocol (the ``__iter__()`` method), or it must support the sequence protocol (the ``__getitem__()`` method with integer arguments starting at 0).

If the second argument, _sentinel_, is given, then _object_ must be a _callable object_. The iterator created in this case will call _object_ with no arguments for each call to its ``__next__()`` method; if the value returned is equal to _sentinel_, ``StopIteration`` will be raised, otherwise the value will be returned.

```python3
>>> # Without sentinel
>>> a = list(range(10))
>>> for i in iter(a): print(i, end='')
0123456789
>>>
>>> # With sentinel
>>> limit = -1
>>> def get():
...     global limit
...     limit += 1
...     return limit
...
>>> for i in iter(get, 5): print(i, end='')
01234
>>> for i in iter(get, 10): print(i, end='')
6789
```


### <a name="len"></a> len( <i>s</i> )

Return the length of an object.

### <a name="list"></a> <i>class</i> list( <i>[iterable]</i> )

### <a name="locals"></a> locals()

Update & Return a dictionary representing the current local symbol table. Free variables are returned by ``locals()`` when it is called in function blocks, but not in class blocks. Note that at the module level, ``locals()`` and ``globals()`` are the same dictionary.


### <a name="map"></a> map( <i>function, iterable, ...</i> )

Return an _iterator_ that applies _function_ to every item of _iterable_, yielding the results. If additional iterable arguments are passed, _function must take that many arguments_ and is applied to the items from all iterables in parallel. With multiple iterables, the _iterator stops when the shortest iterable is exhausted_.

```python3
>>> def show(x, y): return x, y
>>> a
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> b
[10, 11, 12, 13, 14]
>>> list(map(show, a, b))
[(0, 10), (1, 11), (2, 12), (3, 13), (4, 14)]
```

### <a name="max"></a> max( <i>iterable, \*[, key, default]</i> )
### max( <i>arg1, arg2, \*args[, key]</i> )
### <a name="min"></a> min( <i>iterable, \*[, key, default]</i> )
### min( <i>arg1, arg2, \*args[, key]</i> )

Return the largest / smallest item in an iterable or the largest / smallest of two or more arguments.

There are two optional keyword-only arguments. The _key_ argument specifies a _one-argument ordering function_. The _default_ argument specifies an object to return if the provided iterable is empty.


### <a name="next"></a> next(iterator[, default])

Retrieve the next item from the iterator by calling its ``__next__()`` method. If _default_ is given, it is returned if the iterator is exhausted, otherwise ``StopIteration`` is raised.
