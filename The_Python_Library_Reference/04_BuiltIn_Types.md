[Go Back](./README.md)


# <a name="4"></a> 4. Built-In Types

The principal built-in types are numerics, sequences, mappings, classes, instances and exceptions.


## 4.2. Boolean Operations - and, or, not

Boolean operations in ordered by ascending priority:

| Priority | Operation |
| -------- | --------- |
| Highest | x and y |
| Intermediate | x or y |
| Lowest | not x |

``not`` has a lower priority than non-Boolean operators, so ``not a == b`` is interpreted as ``not (a == b)``, and ``a == not b`` is a syntax error.


## 4.3. Comparisons

There are 8 comparison operations. They all have the same priority (which is higher than that of the Boolean operations).
Comparisons _can be chained arbitrarily_; for example, ``x < y <= z`` is equivalent to ``x < y and y <= z``, except that ``y`` is evaluated only once.

This table summarizes the comparison operations:

| Operation | Meaning |
|---|---|
| < | strictly less than |
| <= | less than or equal |
| > | strictly greater than |
| >= | greater than or equal |
| == | equal |
| != | not equal |
| is | object identity |
| is not | negated object identity |

Instances of a class cannot be ordered with respect to other instances of the same class, or other types of object, unless the class defines enough of the methods ``__lt__()``, ``__le__()``, ``__gt__()``, and ``__ge__()``.

The behavior of the ``is`` and ``is not`` operators **cannot be customized**; also they can be applied to any two objects and never raise an exception.

Two more operations with the same syntactic priority, ``in`` and ``not in``, are supported by types that are iterable or implement the ``__contains__()`` method.


## 4.4. Numeric Types — int, float, complex

There are three distinct numeric types: _integers_, _floating point numbers_, and _complex numbers_. In addition, Booleans are a subtype of integers. **Integers have unlimited precision.** Floating point numbers are usually implemented using _double in C_; information about the precision and internal representation of floating point numbers for the machine on which your program is running is available in ``sys.float_info``. Complex numbers have a _real_ and _imaginary_ part, which are each a floating point number. To extract these parts from a complex number ``z``, use ``z.real`` and ``z.imag``.

Python fully supports mixed arithmetic: when a binary arithmetic operator has operands of different numeric types, the operand with the _“narrower” type is widened_ to that of the other, where integer is narrower than floating point, which is narrower than complex. The constructors ``int()``, ``float()``, and ``complex()`` can be used to produce numbers of a specific type.

All numeric types (except complex) support the following operations, sorted by ascending priority (all numeric operations have a higher priority than comparison operations):


| Operation | Result |
|---|---|
| x + y  | sum of x and y |
| x - y | difference of x and y |
| x * y | product of x and y |
| x / y | quotient of x and y |
| x // y | floored quotient of x and y |
| x % y | remainder of x / y |
| -x | x negated |
| +x | x unchanged |
| abs(x) | absolute value or magnitude of x |
| int(x) | x converted to integer |
| float(x) | x converted to floating point |
| complex(re, im) | a complex number with real part re, imaginary part im. im defaults to zero |
| c.conjugate() | conjugate of the complex number c |
| divmod(x, y) | the pair (x // y, x % y) |
| pow(x, y) | x to the power y |
| x ** y | x to the power y |


### 4.4.1 Bitwise Operations on Integer Types

Bitwise operations only make sense for integers. The result of bitwise operations is calculated as though carried out in two’s complement with an infinite number of sign bits.

The priorities of the binary bitwise operations are all lower than the numeric operations and higher than the comparisons; the unary operation ``~`` has the same priority as the other unary numeric operations (``+`` and ``-``).

This table lists the bitwise operations sorted in ascending priority:

| Operation | Result |
|---|---|
| x &#124; y | _bitwise or_ |
| x ^ y | _bitwise exclusive or_ |
| x & y | _bitwise and_ |
| x << n | _left shift_ |
| x >> n | _right shift_ |
| ~x | the bits of x inverted |

_Notes:_
1. Negative shift counts are illegal and cause a ``ValueError`` to be raised
2. A left shift by _n_ bits is equivalent to _multiplication by pow(2, n)_ without overflow check
3. A right shift by _n_ bits is equivalent to _division by pow(2, n)_ without overflow check


### 4.4.2. Additional Methods on Integer Types

The ``int`` type implements the ``numbers.Integral`` abstract base class.

##### <i>int</i>.bit_length()

Return the _number of bits necessary to represent an integer in binary_, excluding the sign and leading zeros:
```python3
>>> n = -37
>>> bin(n)
'-0b100101'
>>> n.bit_length()
6
```


##### <i>int</i>.to_bytes( <i>length, byteorder, \*, signed=False</i> )

Return an _array of bytes_ representing an integer.

```python3
>>> """
... (12).to_bytes(3, byteorder="big") -> b'\x00\x0c'
... 12
... 0000000000001100
... 0000 0000 0000 1100
... \x00 \x0c
... """
>>> (1024).to_bytes(2, byteorder='big')
b'\x04\x00'
>>> (1024).to_bytes(10, byteorder='big')
b'\x00\x00\x00\x00\x00\x00\x00\x00\x04\x00'
>>> (-1024).to_bytes(10, byteorder='big', signed=True)
b'\xff\xff\xff\xff\xff\xff\xff\xff\xfc\x00'
>>> x = 1000
>>> x.to_bytes((x.bit_length() + 7) // 8, byteorder='little')
b'\xe8\x03'
```

The integer is represented using _length_ bytes. An ``OverflowError`` is raised if the integer is not representable with the given number of bytes.

The _byteorder_ argument determines the byte order used to represent the integer. If byteorder is "big" for big-endian, the most significant byte is at the beginning of the byte array. If byteorder is "little" for littel-endian, the most significant byte is at the end of the byte array. To request the _native byte order_ of the host system, use ``sys.byteorder`` as the byte order value.


##### <i>classmethod int</i>.from_bytes( <i>bytes, byteorder, \*, signed=False</i> )

Return the integer represented by the given array of bytes.
```python3
>>> int.from_bytes(b'\x00\x10', byteorder='big')
16
"""
... \x00\x10
... 0000 0000 0001 0000
... 16
"""
>>> int.from_bytes(b'\x00\x10', byteorder='little')
4096
"""
... \x00\x10
... \x10\x00
... 0001 0000 0000 0000
... 4096
"""
```

The argument _bytes_ must either be a bytes-like object or an iterable producing bytes. The _byteorder_ argument determines the byte order used to represent the integer.


### 4.4.3. Additional Methods on Float

The ``float`` type implements the ``numbers.Real`` abstract base class.

##### <i>float</i>.as_integer_ratio()

Return a pair of integers whose ratio is exactly equal to the original float and with a positive denominator. Raises ``OverflowError`` on _infinities_ and a ``ValueError`` on _NaNs_.

```python3
>>> (0.5).as_integer_ratio()
(1, 2)
>>> (0.333333333333333).as_integer_ratio()
(6004799503160655, 18014398509481984)
```

##### <i>float</i>.is_integer()

```python3
>>> (2.5).is_integer()
False
>>> (2.0).is_integer()
True
```

##### <i>float</i>.hex()

Return a representation of a floating-point number as a hexadecimal string. For finite floating-point numbers, this representation will always include a leading _0x_ and a trailing _p_ and exponent.

```python3
>>> (3.14).hex()
'0x1.91eb851eb851fp+1'
```

##### <i>classmethod float</i>.fromhex( <i>s</i> )

Class method to return the float represented by a hexadecimal string _s_. The string _s_ may have leading and trailing whitespace.

Note that ``float.hex()`` is an _instance method_, while ``float.fromhex()`` is a **class method**.

A hexadecimal string takes the form:
```
[sign] ['0x'] integer ['.' fraction] ['p' exponent]
```

where the optional _sign_ may by either ``+ or -``, _integer_ and _fraction_ are strings of hexadecimal digits, and _exponent_ is a decimal integer with an optional leading sign. There must be at least one hexadecimal digit in either the integer or the fraction.

```python3
>>> float.fromhex('0x1.91eb851eb851fp+1')
3.14
```


### 4.4.4. Hasing of Numberic types

For numbers ``x`` and ``y``, possibly of different types, it’s a requirement that ``hash(x) == hash(y)`` whenever ``x == y``. For ease of implementation and efficiency across a variety of numeric types (including ``int``, ``float``, ``decimal.Decimal`` and ``fractions.Fraction``).
