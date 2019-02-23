[Go Back](./README.md)

# <a name="8"></a> 8. Errors and Exceptions

There are (at least) 2 distinguishable kinds of errors:
- syntax errors
- exceptions

## <a name="8_1"></a> 8.1. Syntax Errors

Syntax errors, also known as **parsing errors**, are perhaps the most common kind of complaint you get while you are still learning Python:

```python3
>>> while True print('Hello world')
  File "<stdin>", line 1
    while True print('Hello world')
                   ^
SyntaxError: invalid syntax
```

The parser repeats the offending line and displays a little ‘arrow’ pointing at the earliest point in the line where the error was detected. The error is caused by (or at least detected at) the token preceding the arrow: in the example, the error is detected at the function print(), since a colon (':') is missing before it. File name and line number are printed so you know where to look in case the input came from a script.

## <a name="8_2"></a> 8.2. Exceptions

_Errors detected during execution_ are called _exceptions_. Most exceptions are not handled by programs, however, and result in error messages as shown here:

```python3
>>> 10 * (1/0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> 4 + spam*3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'spam' is not defined
>>> '2' + 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Can't convert 'int' object to str implicitly
```

Exceptions come in different types: the types in the example are ```ZeroDivisionError```, ```NameError``` and ```TypeError```. Standard exception names are built-in identifiers _(not reserved keywords)_.

## <a name="8_3"></a> 8.3. Handling Exceptions

```python3
>>> while True:
...     try:
...         x = int(input("Please enter a number: "))
...         break
...     except ValueError as ve:
...         print("Oops! That was no valid number.  Try again...")
```

The ```try``` statement works as follows.
 - First, the ```try``` clause is executed.
 - If no exception occurs, the ```except``` clause is skipped and execution of the ```try``` statement is finished.
 - If an exception occurs during execution of the ```try``` clause, the rest of the clause is skipped. Then _if its type matches the exception named_ after the ```except``` keyword, the ```except``` clause is executed, and then execution continues after the ```try``` statement.
 - If an exception occurs which does not match the exception named in the ```except``` clause, it is passed on to _outer_ ```try``` statements; if no handler is found, it is an _unhandled exception_ and execution stops.


An ```except``` clause may name **multiple exceptions** as a _parenthesized tuple_, for example:

```python3
... except (RuntimeError, TypeError, NameError):
...     pass
```

A ```class``` in an except clause is compatible with an exception if it is the same class or a base class thereof (but not the other way around — an except clause listing a derived class is not compatible with a base class). For example, the following code will print B, C, D in that order:

```python3
class B(Exception):
    pass

class C(B):
    pass

class D(C):
    pass

for cls in [B, C, D]:
    try:
        raise cls()
    except D:
        print("D")
    except C:
        print("C")
    except B:
        print("B")
```

Note that if the except clauses were reversed (with except B first), it would have printed B, B, B — **the first matching except clause is triggered**.

The ```try … except``` statement has an optional ```else``` clause, which, when present, must follow all except clauses. It is useful for code that must be **executed if the try clause does not raise an exception**.

## <a name="8_4"></a> 8.4. Raising Exceptions

The ```raise``` statement allows the programmer to **force a specified exception** to occur. For example:

```python3
>>> raise NameError('HiThere')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: HiThere
```

The _sole argument_ to ```raise``` indicates the exception to be raised. This must be either an exception instance or an exception class (a class that derives from ```Exception```). If an exception class is passed, it will be implicitly instantiated by calling its constructor.

re-raising an exception (Nested try..except statement)_

```python3
>>> try:
...     raise NameError('HiThere')
... except NameError:
...     print('An exception flew by!')
...     raise
...
An exception flew by!
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
NameError: HiThere
```

## <a name="8_5"></a> 8.5. User-defined Exceptions

Exceptions should typically be derived from the ``Exception`` class, either directly or indirectly.

```python3
class Error(Exception):
    """Base class for exceptions in this module."""
    pass

class InputError(Error):
    """Exception raised for errors in the input.

    Attributes:
        expression -- input expression in which the error occurred
        message -- explanation of the error
    """

    def __init__(self, expression, message):
        self.expression = expression
        self.message = message

class TransitionError(Error):
    """Raised when an operation attempts a state transition that's not
    allowed.

    Attributes:
        previous -- state at beginning of transition
        next -- attempted new state
        message -- explanation of why the specific transition is not allowed
    """

    def __init__(self, previous, next, message):
        self.previous = previous
        self.next = next
        self.message = message
```

## <a name="8_6"></a> 8.6. Defining Clean-up Actions

The ```try``` statement has another optional clause which is intended to define clean-up actions that must be **executed under all circumstances**. For example:

```python3
>>> try:
...     raise KeyboardInterrupt
... finally:
...     print('Goodbye, world!')
...
Goodbye, world!
KeyboardInterrupt
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
```

A ``finally`` clause is always executed before leaving the ``try`` statement, whether an exception has occurred or not. When an exception has occurred in the ``try`` clause and has not been handled by an except clause, it is re-raised after the ``finally`` clause has been executed. The ``finally`` clause is **also executed “on the way out”** when any other clause of the ``try`` statement is left via a ``break``, ``continue`` or ``return`` statement.

In real world applications, the finally clause is useful for releasing external resources (such as files or network connections), regardless of whether the use of the resource was successful.

## <a name="8_7"></a> 8.7. Predefined Clean-up Actions

**Some _objects define_ standard clean-up actions** to be undertaken when the object is no longer needed, regardless of whether or not the operation using the object succeeded or failed. Look at the following example, which tries to open a file and print its contents to the screen.

```python3
for line in open("myfile.txt"):
    print(line, end="")
```

The problem with this code is that it leaves the file open for an indeterminate amount of time after this part of the code has finished executing. The ``with`` statement allows objects like files to be used in a way that ensures they are always cleaned up promptly and correctly.

```python3
with open("myfile.txt") as f:
    for line in f:
        print(line, end="")
```

After the statement is executed, the file ``f`` is always closed, even if a problem was encountered while processing the lines. Objects which, like files, provide predefined clean-up actions will indicate this in their documentation.
