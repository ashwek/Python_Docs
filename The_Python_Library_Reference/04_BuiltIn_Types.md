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

Lists are mutable sequences, typically used to store collections of homogeneous items.


### 4.6.5. Tuples

Tuples are immutable sequences, typically used to store collections of heterogeneous data. Tuples are also used for cases where an immutable sequence of homogeneous data is needed. Note that it is actually the comma which makes a tuple, not the parentheses. The parentheses are optional, except in the empty tuple case, or when they are needed to avoid syntactic ambiguity.


### 4.6.6. Ranges

The range type represents an immutable sequence of numbers and is commonly used for looping a specific number of times in ``for`` loops. The arguments to the range constructor must be integers. If step is zero, ``ValueError`` is raised. Ranges implement all of the common sequence operations except _concatenation_ and _repetition_.

The advantage of the range type over a regular list or tuple is that a **range object will always take the same small amount of memory**, no matter the size of the range it represents (as it only stores the start, stop and step values, calculating individual items and subranges as needed).


## 4.7. Text Sequence Type - str

Textual data in Python is handled with ``str`` objects, or _strings_. Strings are immutable sequences of Unicode code points. String literals are written in a variety of ways:
 - Single quotes: 'allows embedded "double" quotes'
 - Double quotes: "allows embedded 'single' quotes".
 - Triple quoted: '''Three single quotes''', """Three double quotes"""

Triple quoted strings may span multiple lines - all associated whitespace will be included in the string literal.

**String literals that are part of a single expression and have only whitespace between them will be implicitly converted to a single string literal**. That is, ``("spam " "eggs") == "spam eggs"``.

``r`` (“raw”) prefix that disables most escape sequence processing.

```python3
>>> print("hello\nworld")
hello
world
>>> print(r"hello\nworld")
hello\nworld
```

_class_ **str(** _object=''_ **)**

_class_ **str(** _object=b'', encoding='utf-8', errors='strict'_ **)**

``str(object)`` returns ``object.__str__()``, which is the “informal” or nicely printable string representation of object. If object does not have a ``__str__()`` method, then ``str()`` falls back to returning ``repr(object)``.


### 4.7.1. String Methods

Strings implement all of the common sequence operations, along with the additional methods described below.

##### <i>str.</i> capitalize()
Return a copy of the string with its first character capitalized and the rest lowercased.

##### <i>str.</i< casefold()
Return a _casefolded_ copy of the string. Casefolded strings may be used for caseless matching. Casefolding is similar to lowercasing but _more aggressive_ because it is intended to remove all case distinctions in a string. For example, the German lowercase letter 'ß' is equivalent to "ss". Since it is already lowercase, ``lower()`` would do nothing to 'ß'; ``casefold()`` converts it to "ss".

##### <i>str.</i> center( <i>width[, fillchar]</i> )
Return centered in a string of length _width_. Padding is done using the specified _fillchar_ (default is an ASCII space). The _original string is returned if_ _width_ is less than or equal to ``len(s)``.

##### <i>str.</i> count( <i>sub[, start[, end]]</i> )
Return the number of non-overlapping occurrences of substring _sub_ in the range _[start, end]_. Optional arguments _start_ and _end_ are interpreted as in slice notation.

##### <i>str.</i> encode( <i>encoding="utf-8", errors="strict"</i> )
Return an encoded version of the string as a bytes object. Default encoding is 'utf-8'. errors may be given to set a different error handling scheme.

##### <i>str.</i> endswith( <i>suffix[, start[, end]]</i> )
Return ``True`` if the string ends with the specified suffix, otherwise return ``False``. _suffix_ can also be a _tuple of suffixes_ to look for. With optional _start_ and _end_.

##### <i>str.</i> startswith( <i>prefix[, start[, end]]</i> )
Return ``True`` if string starts with the _prefix_, otherwise return ``False``. _prefix_ can also be a tuple of prefixes to look for.

##### <i>str.</i> expandtabs( <i>tabsize=8</i> )
Return a copy of the string where all tab characters are replaced by _one or more spaces_, **depending on the current column and the given tab size**. Tab positions occur every _tabsize_ characters (default is 8, giving tab positions at columns 0, 8, 16 and so on). To expand the string, the current column is set to zero and the string is examined character by character. If the character is a tab (\\t), one or more space characters are inserted in the result until the current column is equal to the next tab position. (The tab character itself is not copied.) If the character is a newline (\\n) or return (\\r), it is copied and the current column is reset to zero. Any other character is copied unchanged and the current column is incremented by one regardless of how the character is represented when printed.

```python3
>>> '01\t012\t0123\t01234'.expandtabs()
'01      012     0123    01234'
>>> '01\t012\t0123\t01234'.expandtabs(4)
'01  012 0123    01234'
```

##### <i>str.</i> find( <i>sub[, start[, end]]</i> )
Return the lowest index in the string where substring _sub_ is found within the slice _s[start:end]_. Optional arguments _start_ and _end_ are interpreted as in slice notation. Return _-1_ if _sub_ is not found.
The ``find()`` method should be used only if you need to know the position of sub. To check if sub is a substring or not, use the ``in`` operator.

##### <i>str.</i> rfind( <i>sub[, start[, end]]</i> )
Return the highest index in the string where substring _sub_ is found, such that _sub_ is contained within _s[start:end]_. returns _-1_ on failure.

##### <i>str.</i> format( <i>\*args, \*\*kwargs</i> )
Perform a string formatting operation. The string on which this method is called can contain literal text or _replacement fields_ delimited by braces ``{}``. Each replacement field contains _either the numeric index_ of a positional argument, or the _name of a keyword argument_. _Returns a copy of the string_ where each replacement field is replaced with the string value of the corresponding argument.

```python3
>>> "The sum of {0} + {1} is {sum}".format(1, 2, sum = 1+2)
'The sum of 1 + 2 is 3'
```

##### <i>str.</i> index( <i>sub[, start[, end]]</i> )
Like ``find()``, but raise ``ValueError`` when the _substring_ is not found.

##### <i>str.</i> rindex( <i>sub[, start[, end]]</i> )
Like ``rfind()`` but raises ``ValueError`` when the substring _sub_ is not found.

##### <i>str.</i> isalnum()
Return ``True`` if all characters in the string are alphanumeric and there is at least one character, ``False`` otherwise.

##### <i>str.</i> isalpha()
Return ``True`` if all characters in the string are alphabetic and there is at least one character, ``False`` otherwise. Alphabetic characters are those characters defined in the Unicode character database.

##### <i>str.</i> isascii()
Return ``True`` if the string is empty or all characters in the string are ASCII, ``False`` otherwise. ASCII characters have code points in the range U+0000-U+007F.

##### <i>str.</i> isdecimal()
Return ``True`` if all characters in the string are decimal characters and there is at least one character, ``False`` otherwise. Decimal characters are those that can be used to form numbers in base 10.

##### <i>str.</i> isdigit()
Return ``True`` if all characters in the string are digits and there is at least one character, ``False`` otherwise.

##### <i>str.</i> isidentifier()
Return ``True`` if the string is a valid identifier according to the language definition. Use ``keyword.iskeyword()`` to test for reserved identifiers such as ``def`` and ``class``.

##### <i>str.</i> islower()
Return ``True`` if all cased characters in the string are lowercase and there is at least one cased character, ``False`` otherwise.

##### <i>str.</i> isnumeric()
Return ``True`` if all characters in the string are numeric characters, and there is at least one character, ``False`` otherwise.

##### <i>str.</i> isprintable()
Return ``True`` if all characters in the string are printable or the string is empty, ``False`` otherwise. Nonprintable characters are those characters defined in the Unicode character database as “Other” or “Separator”.

##### <i>str.</i> isspace()
Return ``True`` if there are only whitespace characters in the string and there is at least one character, ``False`` otherwise.

##### <i>str.</i> istitle()
Return ``True`` if the string is a titlecased string and there is at least one character,.

##### <i>str.</i> isupper()
Return ``True`` if all cased characters in the string are uppercase and there is at least one cased character, ``False`` otherwise.

##### <i>str.</i> join( <i>iterable</i> )
Return a string which is the concatenation of the strings in iterable. A ``TypeError`` will be raised if there are any non-string values in _iterable_. The _separator_ between elements is the string providing this method.

##### <i>str.</i> ljust( <i>width[, fillchar]</i> )
Return the string left justified in a string of length _width_. Padding is done using the specified _fillchar_ (default is an ASCII space). The original string is returned if width is less than or equal to ``len(s)``.

##### <i>str.</i> rjust( <i>width[, fillchar]</i> )
Return the string right justified in a string of length _width_. Padding is done using the specified _fillchar_ (default is an ASCII space). The original string is returned if width is less than or equal to len(s).

##### <i>str.</i> lower()
_Return a copy_ of the string with all the cased characters converted to lowercase.

##### <i>str.</i> upper()
Return a copy of the string with all the cased characters converted to uppercase.

##### <i>str.</i> swapcase()
Return a copy of the string with uppercase characters converted to lowercase and vice versa.

##### <i>str.</i> title()
Return a titlecased version of the string where words start with an uppercase character and the remaining characters are lowercase.

```python3
>>> 'Hello world'.title()
'Hello World'
>>>
>>> "they're bill's friends from the UK".title()
"They'Re Bill'S Friends From The Uk"
```

##### <i>str.</i> lstrip( <i>[chars]</i> )
Return a copy of the string with leading characters removed. The _chars_ argument is a string specifying the **set of characters to be removed**. If omitted or ``None``, the _chars_ argument defaults to removing _whitespace_.

##### <i>str.</i> rstrip( <i>[chars]</i> )
Return a copy of the string with trailing characters removed. The _chars_ argument is a string specifying the **set of characters to be removed**. If omitted or ``None``, the chars argument defaults to removing whitespace.

##### <i>str.</i> strip( <i>[chars]</i> )
Return a copy of the string with the _leading and trailing characters removed_. The _chars_ argument is a string specifying the **set of characters to be removed**.

##### <i>str.</i>partition( <i>sep</i> )
Split the string at the first occurrence of _sep_, and return a _3-tuple_ containing the part _before the separator, the separator itself, and the part after the separator_. If the separator is not found, return a 3-tuple containing the string itself, followed by two empty strings.

##### <i>str.</i> rpartition( <i>sep</i> )
Split the string at the last occurrence of _sep_, and return a 3-tuple.

##### <i>str.</i> replace( <i>old, new[, count]</i> )
Return a copy of the string with all occurrences of substring _old_ replaced by _new_. If the optional argument _count_ is given, only the first count occurrences are replaced.

##### <i>str.</i> rsplit( <i>sep=None, maxsplit=-1</i> )
Return a list of the words in the string, using _sep_ as the delimiter string. If _maxsplit_ is given, at most maxsplit splits are done, the rightmost ones. If sep is not specified or None, any whitespace string is a separator. Except for splitting from the right, ``rsplit()`` behaves like ``split()`` which is described in detail below.

##### <i>str.</i> split( <i>sep=None, maxsplit=-1</i> )
Return a **list of the words** in the string, using _sep_ as the delimiter string. If _maxsplit_ is given, at most maxsplit splits are done (thus, the list will have at most _maxsplit+1_ elements).

```python3
>>> '1,2,3'.split(',')
['1', '2', '3']
>>> '1,2,3'.split(',', maxsplit=1)
['1', '2,3']
>>> '1,2,,3,'.split(',')
['1', '2', '', '3', '']
```

##### <i>str.</i> splitlines( <i>[keepends]</i> )
Return a **list of the lines** in the string, breaking at line boundaries. Line breaks are not included in the resulting list unless _keepends is given and true_.

This method splits on the following line boundaries:

| Representation | Description |
|---|---|
| \\n | Line Feed |
| \\r | Carriage Return |
| \\r\\n | Carriage Return + Line Feed |
| \\v or \\x0b | Line Tabulation |
| \\f or \\x0c | Form Feed |
| \\x1c | File Separator |
| \\x1d | Group Separator |
| \\x1e | Record Separator |
| \\x85 | Next Line (C1 Control Code) |
| \\u2028 | Line Separator |
| \\u2029 | Paragraph Separator |

```python3
>>> 'ab c\n\nde fg\rkl\r\n'.splitlines()
['ab c', '', 'de fg', 'kl']
>>> 'ab c\n\nde fg\rkl\r\n'.splitlines(keepends=True)
['ab c\n', '\n', 'de fg\r', 'kl\r\n']
>>>
>>> "".splitlines()
[]
>>> "One line\n".splitlines()
['One line']
>>>
>>> ''.split('\n')
['']
>>> 'Two lines\n'.split('\n')
['Two lines', '']
```

##### <i>str.</i> translate( <i>table</i> )
Return a copy of the string in which each character has been mapped through the given translation _table_. The table must be an object that implements indexing via ``__getitem__()``, typically a mapping or sequence. When indexed by a Unicode ordinal (an integer), the table object can do any of the following: return a Unicode ordinal or a string, to map the character to one or more other characters; return ``None``, to delete the character from the return string; or raise a ``LookupError`` exception, to map the character to itself.

##### <i>str.</i> zfill( <i>width</i> )
Return a copy of the string left filled with ASCII '0' digits to make a string of length _width_. A leading sign prefix ('+'/'-') is handled by inserting the padding after the sign character rather than before. The original string is returned if width is less than or equal to len(s).

```python3
>>> "42".zfill(5)
'00042'
>>> "-42".zfill(5)
'-0042'
```
