[Go Back](./README.md)

# <a name="3"></a> 3. An Informal Introduction to Python

## <a name="3_1"></a> 3.1. Using Python as a Calculator

### <a name="3_1_1"></a> 3.1.1. Numbers

```python3
int - 2, 4, 6
float - 2.6, 4.9, 6.7
/ - division
// - floor division
% - remainder of division
** - power
```

There is _full support for floating point_; **operators with mixed type operands convert the integer operand to floating point**.

In interactive mode, the last printed expression is assigned to the variable ```_```. This variable _should be treated as read-only_ by the user. Don’t explicitly assign a value to it — you would create an independent local variable with the same name masking the built-in variable with its magic behavior.

```python3
>>> 2 + 1
3
>>> "hello" * _
'hellohellohello'
>>>
```

Python supports other types of numbers, such as Decimal and Fraction _(Python Standard Library)_.

### <a name="3_1_2"></a> 3.1.2. Strings

Strings are **immutable**. The ```print()``` function produces a more readable output, by omitting the enclosing quotes and by printing escaped and special characters.
If you don’t want characters prefaced by ```\``` to be interpreted as special characters, you can use **raw strings** by adding an _**r**_ before the first quote ```r"This is \t tab and \n newline"```

```python3
+ - concatenate
* - repeat
"Py"  "thon"  #2 or more string literals next to each other are automatically concatenated.

# Attempting to use an index that is too large will result in an error
abc = "Text"
>>> abc[42]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range

# However, out of range slice indexes are handled gracefully when used for slicing:
>>> abc[90:]
''
```

### <a name="3_1_3"></a> 3.1.3. Lists

Lists are **mutable**. Python knows a number of _compound data types_, used to group together other values. All *slice operations* return a **new _(shallow copy)_** list containing the requested elements.

```python3
>>> a = [1, 2, 5, 6]
>>> a[1:2] = [2, 3, 4]  # assignment to slice
>>> a
[1, 2, 3, 4, 5, 6]
>>>
```
