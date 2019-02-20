[Go Back](./README.md)

# <a name="4"></a> 4. More Control Flow Tools

## <a name="4_2"></a> 4.2. for Statements

The ```for``` statement in Python _differs a bit_ from what you may be used to in other languages. Rather than always iterating over an arithmetic progression of numbers, or giving the user the ability to define both the iteration step and halting condition, **Python’s for statement iterates over the items of any sequence** (a list or a string), in the order that they appear in the sequence.

_If you need to modify the sequence you are iterating over while inside the loop, it is recommended that you first make a copy. Iterating over a sequence does not implicitly make a copy. (making any changes to the sequence may change the loop iterations)_

```python3
for i in a[:]:
    # loop body
```

## <a name="4_3"></a> 4.3. The range() Function

The built-in function ```range()``` generates arithmetic progressions to iterate over a sequence of numbers.

```python3
>>> print(range(10))
range(0, 10)
```

In many ways the object returned by ```range()``` behaves as if it is a list, but in fact it isn’t. _It is an **object**_ which returns the successive items of the desired sequence when you iterate over it, but it doesn’t really make the list, thus **saving space**.

## <a name="4_4"></a> 4.4. break and continue Statements, and else Clauses on Loops

Loop statements may have an **else** clause; it is executed when the loop terminates through exhaustion of the list or when the condition becomes false, but _not when the loop is terminated by a **break** statement_.

## <a name="4_5"></a> 4.5. pass Statements

The ```pass``` statement does nothing. It can be used when a statement is required syntactically but the program requires no action.

## <a name="4_6"></a> 4.6. Defining Functions

The keyword ```def``` introduces a function _definition_.

The _first statement_ of the function body can optionally be a _string literal_; this string literal is the function’s **documentation string, or docstring**.

The execution of a function introduces a _new symbol table_ used for the local variables of the function. Variable references first look in the local symbol table, then in the local symbol tables of enclosing functions, then in the global symbol table, and finally in the table of built-in names.

The actual parameters (arguments) to a function call are introduced in the local symbol table of the called function when it is called; thus, **arguments are passed using call by value** _(where the value is always an object reference, not the value of the object)_.

**A function definition introduces the function name in the current symbol table.** The value of the function name has a type that is recognized by the interpreter as a user-defined function. This value can be assigned to another name which can then also be used as a function.

Functions without a return statement return ```None```. The ```return``` without an expression argument returns ```None```.

## <a name="4_7"></a> 4.7. More on Defining Functions

### <a name="4_7_1"></a> 4.7.1. Default Argument Values

The default values are evaluated at the point of function definition in the defining scope, so that

```python3
i = 5

def f(arg=i):
    print(arg)

i = 6
f()
```

will print 5.

**Important warning: _The default value is evaluated only once. This makes a difference when the default is a mutable object such as a list, dictionary, or instances of most classes._**

For example, the following function accumulates the arguments passed to it on subsequent calls:

```python3
def f(a, L=[]):
    L.append(a)
    return L

print(f(1))
print(f(2))
print(f(3))

# This will print

[1]
[1, 2]
[1, 2, 3]
```

### <a name="4_7_2"></a> 4.7.2. Keyword Arguments

```python3
def sum(a, b):
    print(a + b)

sum(5, 6)
sum(a = 5, b = 6)
sum(b = 9, a = 0)
sum(9, b = 9)
sum(a = 8, 7)  # Error
sum(9, a = 0)  # Error
sum(b = 8, 8)  # Error
```

### <a name="4_7_3"></a> 4.7.3. Arbitrary Argument Lists

We can specify a function that can be called with **an arbitrary number of arguments**. These arguments will be **wrapped up in a tuple**. Normally, these **variadic** arguments _will be last in the list of formal parameters_, because they scoop up all remaining input arguments that are passed to the function. Any formal parameters which occur after the ```*args``` parameter are _keyword-only_ arguments, meaning that they can only be used as keywords rather than positional arguments.

### <a name="4_7_4"></a> 4.7.4. Unpacking Argument Lists

The reverse situation occurs when the _arguments_ are already in a list or tuple but _need to be unpacked for a function call_ requiring separate positional arguments.

### <a name="4_7_5"></a> 4.7.5. Lambda Expressions

Small anonymous functions can be created with the ```lambda``` keyword. This function returns the sum of its two arguments: ```lambda a, b: a+b```. Lambda functions can be used wherever function objects are required. They are **syntactically restricted to a single expression**.

### <a name="4_7_6"></a> 4.7.6. Documentation Strings

The first line should always be a short, concise summary of the object’s purpose. If there are more lines in the documentation string, the second line should be blank, visually separating the summary from the rest of the description. The following lines should be one or more paragraphs describing the object’s calling conventions, its side effects, etc.

```python3
>>> def my_function():
...     """Do nothing, but document it.
...
...     No, really, it doesn't do anything.
...     """
...     pass
...
>>> print(my_function.__doc__)
Do nothing, but document it.

    No, really, it doesn't do anything.
```

### <a name="4_7_7"></a> 4.7.7. Function Annotations

Function annotations are completely optional **metadata information about the types used by user-defined functions**.
Annotations are stored in the ```__annotations__``` attribute of the function as a **dictionary** and have no effect on any other part of the function.

**Parameter annotations** are defined by a _colon after the parameter name_, followed by an expression evaluating to the value of the annotation. **Return annotations** are defined by a literal ->, followed by an expression, between the parameter list and the colon denoting the end of the def statement.

```python3
>>> def mix(text: str, count: int = 1) -> list:
...     return [text] * count
...

>>> mix("abc", 2)
['abc', 'abc']

>>> mix.__annotations__
{'text': <class 'str'>, 'count': <class 'int'>, 'return': <class 'list'>}
```

## <a name="4_8"></a> 4.8. Intermezzo: Coding Style

**PEP 8** has emerged as the style guide that most projects adhere to; it promotes a very readable and eye-pleasing coding style. Some of the most important points:
 - Use 4-space indentation, and no tabs.
 - Wrap lines so that they don’t exceed 79 characters.
 - Use blank lines to separate functions and classes, and larger blocks of code inside functions.
 - When possible, put comments on a line of their own.
 - Use docstrings.
 - Use spaces around operators and after commas, but not directly inside bracketing constructs: a = f(1, 2) + g(3, 4).
 - Name your classes and functions consistently. CamelCase for classes and lower_case_with_underscores for functions and methods.
 - Don’t use fancy encodings if your code is meant to be used in international environments. Python’s default, UTF-8, or even plain ASCII work best in any case.
