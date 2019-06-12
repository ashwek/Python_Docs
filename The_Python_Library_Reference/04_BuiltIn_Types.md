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



## 4.5. Iterator Types

Python supports a concept of iteration over containers. This is _implemented using two distinct methods_; these are used to allow user-defined classes to support iteration.

One method needs to be defined for _container objects_ to provide iteration support:

##### <i>container</i>.\_\_iter\_\_()

Return an _iterator object_. The object is required to support the iterator protocol described below. If a container supports different types of iteration, additional methods can be provided to specifically request iterators for those iteration types.

The _iterator objects_ themselves are required to support the following two methods, which together form the iterator protocol:

##### <i>iterator</i>.\_\_iter\_\_()

Return the iterator object itself. This is required to allow both containers and iterators to be used with the ``for`` and ``in`` statements.

##### <i>iterator</i>.\_\_next\_\_()

Return the next item from the container. If there are no further items, raise the ``StopIteration`` exception.

### 4.5.1. Generator Types

Python’s generators provide a convenient way to implement the iterator protocol. If a container object’s ``__iter__()`` method is implemented as a _generator_, it will automatically return an iterator object supplying the ``__iter__()`` and ``__next__()`` methods.


## 4.6. Sequence Types - list, tuple, range

There are three basic sequence types:
 - lists
 - tuples
 - range

### 4.6.1. Commom Sequence operations

This table lists the sequence operations sorted in ascending priority. In the table:
 - _s_ and _t_ are sequences of the same type
 - _n_, _i_, _j_ and _k_ are integers
 - _x_ is an arbitrary object that meets any type and value restrictions imposed by _s_

The ``in`` and ``not in`` operations have the same priorities as the comparison operations. The ``+`` (concatenation) and ``*`` (repetition) operations have the same priority as the corresponding numeric operations.

| Operation | Result |
|---|---|
| x in s | True if an item of s is equal to x, else False |
| x not in s | False if an item of s is equal to x, else True |
| s + t | the concatenation of s and t |
| s * n or n * s | equivalent to adding s to itself n times |
| s[i] | ith item of s, origin 0 |
| s[i:j] | slice of s from i to j |
| s[i:j:k] | slice of s from i to j with step k |
| len(s) | length of s |
| min(s) | smallest item of s |
| max(s) | largest item of s |
| s.index(x[, i[, j]]) | index of the first occurrence of x in s (at or after index i and before index j) |
| s.count(x) | total number of occurrences of x in s |

When using the ``*`` repetition operator, the items in the sequence are **not copied**, they are **referenced** multiple times.

```python3
>>> lists = [[]] * 3
>>> lists
[[], [], []]
>>> lists[0].append(3)
>>> lists
[[3], [3], [3]]
```

Some sequence types (such as ``range``) only support item sequences that follow _specific patterns_, and hence don’t support sequence concatenation or repetition.


### 4.6.2. Immutable Sequence Types

The only operation that immutable sequence types generally implement that is not also implemented by mutable sequence types is support for the ``hash()`` built-in.

This support allows immutable sequences, such as tuple instances, to be used as dict keys and stored in set and frozenset instances.

_An object is hashable if it has a hash value which never changes during its lifetime (it needs a ``hash()`` method)._


### 4.6.3. Mutable Sequence Types

In the table:
 - *s* is an instance of a mutable sequence type
 - *t* is any iterable object
 - *x* is an arbitrary object that meets any type and value restrictions imposed by *s*


| Operation | Result |
|---|---|
| s[i] = x | item i of s is replaced by x |
| s[i:j] = t | slice of s from i to j is replaced by the contents of the iterable t |
| del s[i:j] | same as s[i:j] = [] |
| s[i:j:k] = t | the elements of s[i:j:k] are replaced by those of t |
| del s[i:j:k] | removes the elements of s[i:j:k] from the list |
| s.append(x) | appends x to the end of the sequence (same as s[len(s):len(s)] = [x]) |
| s.clear() | removes all items from s (same as del s[:]) |
| s.copy() | creates a shallow copy of s (same as s[:]) |
| s.extend(t) or s += t | extends s with the contents of t (for the most part the same as s[len(s):len(s)] = t) |
| s *= n | updates s with its contents repeated n times |
| s.insert(i, x) | inserts x into s at the index given by i (same as s[i:i] = [x]) |
| s.pop([i]) | retrieves the item at i and also removes it from s |
| s.remove(x) | remove the first item from s where s[i] is equal to x |
| s.reverse() | reverses the items of s in place |


### 4.6.4. Lists
