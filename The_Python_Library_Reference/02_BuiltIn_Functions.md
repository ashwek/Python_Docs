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
