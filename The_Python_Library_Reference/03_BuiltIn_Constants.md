[Go Back](./README.md)

# <a name="3"></a> 3. Built-In Constants


### False

The ``False`` value of the ``bool`` type. Assignments to ``False`` are illegal and raise a ``SyntaxError``.


### True

The ``True`` value of the ``bool`` type. Assignments to ``True`` are illegal and raise a ``SyntaxError``.


### None

The sole value of the type ``NoneType``. ``None`` is frequently used to represent the absence of a value, as when default arguments are not passed to a function. Assignments to ``None`` are illegal and raise a ``SyntaxError``.


### NotImplemented

Special value which should be returned by the binary special methods (e.g. ``__eq__()``, ``__lt__()``, ``__add__()``, ``__rsub__()``, etc.) to indicate that the operation is not implemented. Its truth value is ``True``.

When a binary (or in-place) method returns ``NotImplemented`` the interpreter will try the reflected operation on the other type. If all attempts return ``NotImplemented``, the interpreter will raise an appropriate exception.


### Ellipsis

The same as the ellipsis literal ``...``. Special value used mostly in conjunction with extended slicing syntax for user-defined container data types.

### \_\_debug\_\_

This constant is true if Python was not started with an -O option.
