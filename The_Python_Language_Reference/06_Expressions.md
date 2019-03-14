[Go Back](./README.md)

# <a name="6"></a> 6. Expressions

This chapter explains the meaning of the elements of expressions in Python.

_Syntax Notes:_ In this and the following chapters, **extended BNF notation** will be used to describe syntax, not lexical analysis. When (one alternative of) a syntax rule has the form

``name ::=  othername``

and no semantics are given, the semantics of this form of ``name`` are the same as for ``othername``.

## <a name="6_1"></a> 6.1. Arithmetic conversions

When a description of an arithmetic operator below uses the phrase “the numeric arguments are converted to a common type,” this means that the operator implementation for built-in types works as follows:
- If either argument is a complex number, the other is converted to complex;
- otherwise, if either argument is a floating point number, the other is converted to floating point;
- otherwise, both must be integers and no conversion is necessary.

Some additional rules apply for certain operators (e.g., a string as a left argument to the ‘%’ operator). Extensions must define their own conversion behavior.

## <a name="6_2"></a> 6.2. Atoms

Atoms are the most basic elements of expressions. The simplest atoms are identifiers or literals. Forms enclosed in parentheses, brackets or braces are also categorized syntactically as atoms. The syntax for atoms is:

```
atom      ::=  identifier | literal | enclosure
enclosure ::=  parenth_form | list_display | dict_display | set_display
               | generator_expression | yield_atom
```

### <a name="6_2_1"></a> 6.2.1. Identifiers (Names)

When the name is bound to an object, evaluation of the atom yields that object. When a name is not bound, an attempt to evaluate it raises a ``NameError`` exception.

_Private name mangling:_ When an identifier that textually occurs in a class definition begins with _two or more underscore_ characters and does not end in two or more underscores, it is considered a private name of that class. The transformation inserts the class name, with leading underscores removed and a single underscore inserted, in front of the name. For example, the identifier ``__spam`` occurring in a class named ``Ham`` will be transformed to ``_Ham__spam``.

### <a name="6_2_2"></a> 6.2.2. Literals

Python supports string and bytes literals and various numeric literals:

```
literal ::=  stringliteral | bytesliteral
             | integer | floatnumber | imagnumber
```

Evaluation of a literal yields an object of the given type with the given value. The value may be approximated in the case of floating point and imaginary literals.

**All literals correspond to immutable data types**, and hence the object’s identity is less important than its value. Multiple evaluations of literals with the same value may obtain the same object or a different object with the same value.

### <a name="6_2_3"></a> 6.2.3. Parenthesized forms

A parenthesized form is an optional expression list enclosed in parentheses:

```
parenth_form ::=  "(" [starred_expression] ")"
```

A parenthesized expression list yields whatever that expression list yields: if the list contains at least one comma, it yields a tuple; otherwise, it yields the single expression that makes up the expression list.

An empty pair of parentheses yields an empty tuple object. Since tuples are immutable, the rules for literals apply.

_**Note that tuples are not formed by the parentheses, but rather by use of the comma operator.**_ The exception is the empty tuple, for which parentheses are required — allowing unparenthesized “nothing” in expressions would cause ambiguities and allow common typos to pass uncaught.

### <a name="6_2_4"></a> 6.2.4. Displays for lists, sets and dictionaries

For constructing a list, a set or a dictionary Python provides special syntax called “displays”, each of them in two flavors:
- either the container contents are listed explicitly, or
- they are computed via a set of looping and filtering instructions, called a comprehension.

```
comprehension ::=  expression comp_for
comp_for      ::=  ["async"] "for" target_list "in" or_test [comp_iter]
comp_iter     ::=  comp_for | comp_if
comp_if       ::=  "if" expression_nocond [comp_iter]
```

The comprehension consists of _a single expression_ followed by _at least one ``for`` clause_ and _zero or more ``for`` or ``if`` clauses_. In this case, the elements of the new container are those that would be produced by considering each of the ``for`` or ``if`` clauses a block, **nesting from left to right, and evaluating the expression to produce an element each time the innermost block is reached**.

However, aside from the iterable expression in the leftmost for clause, the comprehension is executed in a separate implicitly nested scope. This ensures that names assigned to in the target list don’t “leak” into the enclosing scope.

The iterable expression in the leftmost ``for`` clause is evaluated directly in the enclosing scope and then _passed as an argument to the implictly nested scope_. Subsequent ``for`` clauses and any filter condition in the leftmost ``for`` clause cannot be evaluated in the enclosing scope as they may depend on the values obtained from the leftmost iterable. For example: ``[x*y for x in range(10) for y in range(x, x+10)]``.

To ensure the comprehension always results in a container of the appropriate type, ``yield`` and ``yield from`` expressions are _prohibited in the implicitly nested scope_.

### <a name="6_2_5"></a> 6.2.5. List displays

A list display is a possibly empty series of expressions enclosed in square brackets:

```
list_display ::=  "[" [starred_list | comprehension] "]"
```

A list display yields a new list object, the contents being specified by either a list of expressions or a comprehension. When a comma-separated list of expressions is supplied, its elements are evaluated from left to right and placed into the list object in that order. When a comprehension is supplied, the list is constructed from the elements resulting from the comprehension.

### <a name="6_2_6"></a> 6.2.6. Set displays

A set display is _denoted by curly braces_ and _distinguishable from dictionary displays by the lack of colons separating keys and values_:

```
set_display ::=  "{" (starred_list | comprehension) "}"
```

A set display yields a new mutable set object, the contents being specified by either a sequence of expressions or a comprehension. When a comma-separated list of expressions is supplied, its elements are evaluated from left to right and added to the set object. When a comprehension is supplied, the set is constructed from the elements resulting from the comprehension.

An empty set **cannot** be constructed with ``{}``; this literal constructs an empty dictionary.

### <a name="6_2_7"></a> 2.7. Dictionary displays

A dictionary display is a possibly empty series of key/datum pairs enclosed in curly braces:

```
dict_display       ::=  "{" [key_datum_list | dict_comprehension] "}"
key_datum_list     ::=  key_datum ("," key_datum)* [","]
key_datum          ::=  expression ":" expression | "**" or_expr
dict_comprehension ::=  expression ":" expression comp_for
```

A dictionary display yields a new dictionary object. If a comma-separated sequence of key/datum pairs is given, they are evaluated from left to right to define the entries of the dictionary: each key object is used as a key into the dictionary to store the corresponding datum. _This means that you can specify the same key multiple times in the key/datum list, and the final dictionary’s value for that key will be the last one given_.

A double asterisk ``**`` denotes **_dictionary unpacking_**. Its operand must be a mapping. Each mapping item is added to the new dictionary.

A dict comprehension, in contrast to list and set comprehensions, needs two expressions separated with a colon followed by the usual “for” and “if” clauses. When the comprehension is run, the resulting key and value elements are inserted in the new dictionary in the order they are produced. Clashes between duplicate keys are not detected; the last datum stored for a given key value prevails.

### <a name="6_2_8"></a> 6.2.8. Generator expressions

A generator expression is a compact generator notation in parentheses:

```
generator_expression ::=  "(" expression comp_for ")"
```

A generator expression yields a new generator object. Its syntax is the same as for comprehensions, except that it is enclosed in parentheses instead of brackets or curly braces.

Variables used in the generator expression are evaluated lazily when the ``__next__()`` method is called for the generator object. However, the iterable expression in the leftmost ``for`` clause is immediately evaluated, so that an error produced by it will be emitted at the point where the generator expression is defined, rather than at the point where the first value is retrieved. Subsequent ``for`` clauses and any filter condition in the leftmost ``for`` clause cannot be evaluated in the enclosing scope as they may depend on the values obtained from the leftmost iterable. For example: ``(x*y for x in range(10) for y in range(x, x+10))``.

To avoid interfering with the expected operation of the generator expression itself, ``yield`` and ``yield from`` expressions are prohibited in the implicitly defined generator.

If a generator expression contains either ``async for`` clauses or ``await`` expressions it is called an asynchronous generator expression. An asynchronous generator expression returns a new asynchronous generator object, which is an asynchronous iterator.

### <a name="6_2_9"></a> 6.2.9. Yield expressions

```
yield_atom       ::=  "(" yield_expression ")"
yield_expression ::=  "yield" [expression_list | "from" expression]
```

The ``yield`` expression is used when defining a generator function or an asynchronous generator function and thus can only be used in the body of a function definition. Using a ``yield`` expression in a function’s body causes that function to be a generator, and using it in an ``async def`` function’s body causes that coroutine function to be an asynchronous generator. For example:

```python3
def gen():  # defines a generator function
    yield 123

async def agen(): # defines an asynchronous generator function
    yield 123
```

When a generator function is called, it returns an iterator known as a generator. That generator then controls the execution of the generator function. The execution starts when one of the generator’s methods is called. At that time, the execution proceeds to the first ``yield`` expression, where it is suspended again, returning the value of _expression_list_ to the generator’s caller. By suspended, we mean that all local state is retained, including the current bindings of local variables, the instruction pointer, the internal evaluation stack, and the state of any exception handling. When the execution is resumed by calling one of the generator’s methods, the function can proceed exactly as if the ``yield`` expression were just another external call. The value of the ``yield`` expression after resuming depends on the method which resumed the execution. If ``__next__()`` is used (typically via either a ``for`` or the ``next()`` builtin) then the result is ``None``.

All of this makes generator functions quite similar to _coroutines_; they yield multiple times, **they have more than one entry point and their execution can be suspended**. The only difference is that a generator function cannot control where the execution should continue after it yields; the control is always transferred to the generator’s caller.

When the underlying iterator is complete, the value attribute of the raised ``StopIteration`` instance becomes the value of the yield expression.

#### <a name="6_2_9_1"></a> 6.2.9.1. Generator-iterator methods

This subsection describes the methods of a generator iterator. They can be used to control the execution of a generator function.

##### generator.\_\_next\_\_()
Starts the execution of a generator function or resumes it at the last executed ``yield`` expression. When a generator function is resumed with a ``__next__()`` method, the **current yield expression** always evaluates to ``None``. The execution then continues to the next yield expression, where the generator is suspended again, and the value of the expression_list is returned to ``__next__()``’s caller. If the generator exits without yielding another value, a ``StopIteration`` exception is raised. This method is normally called implicitly by a ``for`` loop or by the built-in ``next()`` function.

##### generator.send(value)
Resumes the execution and “sends” a value into the generator function. The value argument becomes the result of the **current ``yield`` expression**. The ``send()`` method returns the next value yielded by the generator, or raises ``StopIteration`` if the generator exits without yielding another value. When ``send()`` is called to start the generator, it must be called with ``None`` as the argument, because there is no yield expression that could receive the value.

##### generator.throw(type[, value[, traceback]])
Raises an exception of type ``type`` at the point where the generator was paused, and returns the next value yielded by the generator function. If the generator exits without yielding another value, a ``StopIteration`` exception is raised. If the generator function does not catch the passed-in exception, or raises a different exception, then that exception propagates to the caller.

##### generator.close()
Raises a ``GeneratorExit`` at the point where the generator function was paused. If the generator function then exits gracefully, is already closed, or raises ``GeneratorExit``, close returns to its caller. If the generator yields a value, a ``RuntimeError`` is raised. If the generator raises any other exception, it is propagated to the caller. ``close()`` does nothing if the generator has already exited due to an exception or normal exit.

```python3
>>> def echo(value=None):
...     print("Execution starts when 'next()' is called for the first time.")
...     try:
...         while True:
...             try:
...                 value = (yield value)
...             except Exception as e:
...                 value = e
...     finally:
...         print("Don't forget to clean up when 'close()' is called.")
...
>>> generator = echo(1)
>>> print(next(generator))
Execution starts when 'next()' is called for the first time.
1
>>> print(next(generator))
None
>>> print(generator.send(2))
2
>>> generator.throw(TypeError, "spam")
TypeError('spam',)
>>> generator.close()
Don't forget to clean up when 'close()' is called.
```

#### <a name="6_2_9_3"></a> 6.2.9.3. Asynchronous generator functions

The presence of a ``yield`` expression in a function or method defined using ``async def`` further defines the function as a _asynchronous generator function_.

When an asynchronous generator function is called, it returns an asynchronous iterator known as an asynchronous generator object. That object then controls the execution of the generator function. An asynchronous generator object is typically used in an ``async for`` statement in a _coroutine_ function analogously to how a generator object would be used in a ``for`` statement.

Calling one of the asynchronous generator’s methods returns an _awaitable_ object, and the execution starts when this object is awaited on. At that time, the execution proceeds to the first yield expression, where it is suspended again, returning the value of expression_list to the awaiting coroutine. As with a generator, suspension means that all local state is retained, including the current bindings of local variables, the instruction pointer, the internal evaluation stack, and the state of any exception handling. When the execution is resumed by awaiting on the next object returned by the asynchronous generator’s methods, the function can proceed exactly as if the yield expression were just another external call. The value of the yield expression after resuming depends on the method which resumed the execution. If ``__anext__()`` is used then the result is ``None``. Otherwise, if ``asend()`` is used, then the result will be the value passed in to that method.

#### <a name="6_2_9_4"></a> 6.2.9.4. Asynchronous generator-iterator methods

This subsection describes the methods of an asynchronous generator iterator, which are used to control the execution of a generator function.

##### coroutine agen.\_\_anext\_\_()
Returns an awaitable which when run starts to execute the asynchronous generator or resumes it at the last executed yield expression. When an asynchronous generator function is resumed with a ``__anext__()`` method, the current yield expression always evaluates to ``None`` in the returned awaitable, which when run will continue to the next yield expression. The value of the expression_list of the yield expression is the value of the ``StopIteration`` exception raised by the completing coroutine. If the asynchronous generator exits without yielding another value, the awaitable instead raises an ``StopAsyncIteration`` exception, signalling that the asynchronous iteration has completed.

##### coroutine agen.asend(value)
Returns an awaitable which when run resumes the execution of the asynchronous generator. As with the ``send()`` method for a generator, this “sends” a value into the asynchronous generator function, and the value argument becomes the result of the current yield expression. The awaitable returned by the ``asend()`` method will return the next value yielded by the generator as the value of the raised ``StopIteration``, or raises ``StopAsyncIteration`` if the asynchronous generator exits without yielding another value. When ``asend()`` is called to start the asynchronous generator, it must be called with ``None`` as the argument, because there is no yield expression that could receive the value.

##### coroutine agen.athrow(type[, value[, traceback]])
Returns an awaitable that raises an exception of type type at the point where the asynchronous generator was paused, and returns the next value yielded by the generator function as the value of the raised ``StopIteration`` exception. If the asynchronous generator exits without yielding another value, an ``StopAsyncIteration`` exception is raised by the awaitable. If the generator function does not catch the passed-in exception, or raises a different exception, then when the awaitable is run that exception propagates to the caller of the awaitable.

##### coroutine agen.aclose()
Returns an awaitable that when run will throw a ``GeneratorExit`` into the asynchronous generator function at the point where it was paused. If the asynchronous generator function then exits gracefully, is already closed, or raises ``GeneratorExit``, then the returned awaitable will raise a ``StopIteration`` exception. Any further awaitables returned by subsequent calls to the asynchronous generator will raise a ``StopAsyncIteration`` exception. If the asynchronous generator yields a value, a ``RuntimeError`` is raised by the awaitable. If the asynchronous generator raises any other exception, it is propagated to the caller of the awaitable. If the asynchronous generator has already exited due to an exception or normal exit, then further calls to ``aclose()`` will return an awaitable that does nothing.

## <a name="6_3"></a> 6.3. Primaries

Primaries represent the _most tightly bound operations_ of the language. Their syntax is:

```
primary ::=  atom | attributeref | subscription | slicing | call
```

### <a name="6_3_1"></a> 6.3.1. Attribute references

An attribute reference is a primary followed by a period and a name:

```
attributeref ::=  primary "." identifier
```

The primary must evaluate to an object of a type that supports attribute references, which most objects do. _This object is then asked to produce the attribute whose name is the identifier._ This production can be customized by overriding the ``__getattr__()`` method. If this attribute is not available, the exception ``AttributeError`` is raised. Otherwise, the type and value of the object produced is determined by the object. Multiple evaluations of the same attribute reference may yield different objects.

### <a name="6_3_2"></a> 6.3.2. Subscriptions

A subscription selects an item of a sequence (string, tuple or list) or mapping (dictionary) object:

```
subscription ::=  primary "[" expression_list "]"
```

The _primary_ must evaluate to an object that supports subscription (lists or dictionaries for example). User-defined objects can support subscription by defining a ``__getitem__()`` method. A string’s items are characters. A character is not a separate data type but a string of exactly one character.

### <a name="6_3_3"></a> 6.3.3. Slicings

A slicing _selects a range of items in a sequence object_ (e.g., a string, tuple or list). Slicings may be _used as expressions_ or _as targets in assignment_ or ``del`` statements. The syntax for a slicing:

```
slicing      ::=  primary "[" slice_list "]"
slice_list   ::=  slice_item ("," slice_item)* [","]
slice_item   ::=  expression | proper_slice
proper_slice ::=  [lower_bound] ":" [upper_bound] [ ":" [stride] ]
lower_bound  ::=  expression
upper_bound  ::=  expression
stride       ::=  expression
```

There is ambiguity in the formal syntax here: anything that looks like an expression list also looks like a slice list, so any subscription can be interpreted as a slicing. Rather than further complicating the syntax, this is disambiguated by defining that in this case the interpretation as a subscription takes priority over the interpretation as a slicing.

The semantics for a slicing are as follows. The primary is indexed (using the same ``__getitem__()`` method as normal subscription) with a key that is constructed from the slice list, as follows. If the slice list contains at least one comma, the key is a tuple containing the conversion of the slice items; otherwise, the conversion of the lone slice item is the key. The conversion of a slice item that is an expression is that expression. The conversion of a proper slice is a slice object whose start, stop and step attributes are the values of the expressions given as lower bound, upper bound and stride, respectively, substituting ``None`` for missing expressions.

### <a name="6_3_4"></a> 6.3.4. Calls

A call calls a callable object (e.g., a function) with a possibly empty series of arguments:

```
call                 ::=  primary "(" [argument_list [","] | comprehension] ")"
argument_list        ::=  positional_arguments ["," starred_and_keywords]
                            ["," keywords_arguments]
                          | starred_and_keywords ["," keywords_arguments]
                          | keywords_arguments
positional_arguments ::=  ["*"] expression ("," ["*"] expression)*
starred_and_keywords ::=  ("*" expression | keyword_item)
                          ("," "*" expression | "," keyword_item)*
keywords_arguments   ::=  (keyword_item | "**" expression)
                          ("," keyword_item | "," "**" expression)*
keyword_item         ::=  identifier "=" expression
```

An optional trailing comma may be present after the positional and keyword arguments but does not affect the semantics.

The primary must evaluate to a callable object (user-defined functions, built-in functions, methods of built-in objects, class objects, methods of class instances, and all objects having a ``__call__()`` method are callable). All argument expressions are evaluated before the call is attempted.

If keyword arguments are present, they are first converted to positional arguments, as follows. First, a list of unfilled slots is created for the formal parameters. If there are N positional arguments, they are placed in the first N slots. Next, for each keyword argument, the identifier is used to determine the corresponding slot _(if the identifier is the same as the first formal parameter name, the first slot is used, and so on)_. If the slot is already filled, a ``TypeError`` exception is raised. Otherwise, the value of the argument is placed in the slot, filling it (even if the expression is ``None``, it fills the slot). When all arguments have been processed, the slots that are still unfilled are filled with the corresponding default value from the function definition. (**Default values are calculated, once, when the function is defined**; thus, a mutable object such as a list or dictionary used as default value will be shared by all calls that don’t specify an argument value for the corresponding slot; **this should usually be avoided**.) If there are any unfilled slots for which no default value is specified, a ``TypeError`` exception is raised. Otherwise, the list of filled slots is used as the argument list for the call.

If there are more positional arguments than there are formal parameter slots, a ``TypeError`` exception is raised, unless a _formal parameter_ using the syntax ``*identifier`` is present; in this case, that formal parameter receives a tuple containing the excess positional arguments (or an empty tuple if there were no excess positional arguments).

If any keyword argument does not correspond to a formal parameter name, a ``TypeError`` exception is raised, unless a _formal parameter_ using the syntax ``**identifier`` is present; in this case, that formal parameter receives a dictionary containing the excess keyword arguments (using the keywords as keys and the argument values as corresponding values), or a (new) empty dictionary if there were no excess keyword arguments.

If the syntax ``*expression`` appears in the function call, expression must evaluate to an iterable. Elements from these iterables are treated as if they were additional positional arguments. For the call ``f(x1, x2, *y, x3, x4)``, if ``y`` evaluates to a sequence ``y1, …, yM``, this is equivalent to a call with ``M+4`` positional arguments ``x1, x2, y1, …, yM, x3, x4``.

A consequence of this is that although the ``*expression`` syntax may appear after explicit keyword arguments, **it is processed before the keyword arguments** (and any ``**expression`` arguments – see below). So:

```python3
>>> def f(a, b):
...     print(a, b)
...
>>> f(b=1, *(2,))
2 1
>>> f(a=1, *(2,))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f() got multiple values for keyword argument 'a'
>>> f(1, *(2,))
1 2
```

It is _unusual_ for both keyword arguments and the ``*expression`` syntax to be used in the same call, so in practice this confusion does not arise.

If the syntax ``**expression`` appears in the function call, expression must evaluate to a mapping, the contents of which are treated as additional keyword arguments. If a keyword is already present (as an explicit keyword argument, or from another unpacking), a ``TypeError`` exception is raised.

_Formal parameters_ using the syntax ``*identifier`` or ``**identifier`` cannot be used as positional argument slots or as keyword argument names.

A call always returns some value, possibly ``None``, unless it raises an exception. How this value is computed depends on the type of the callable object.

If it is—

- _a user-defined function:_
The code block for the function is executed, passing it the argument list. The first thing the code block will do is bind the formal parameters to the arguments. When the code block executes a return statement, this specifies the return value of the function call.

- _a built-in function or method:_
The result is up to the interpreter.

- _a class object:_
A new instance of that class is returned.

- _a class instance method:_
The corresponding user-defined function is called, with an argument list that is one longer than the argument list of the call: the instance becomes the first argument.

- _a class instance:_
The class must define a ``__call__()`` method; the effect is then the same as if that method was called.

## <a name="6_4"></a> 6.4. Await expression

Suspend the execution of coroutine on an awaitable object. Can only be used inside a coroutine function.
```
await_expr ::=  "await" primary
```

## <a name="6_5"></a> 6.5. The power operator

The power operator binds more tightly than unary operators on its left; it binds less tightly than unary operators on its right. The syntax is:

```
power ::=  (await_expr | primary) ["**" u_expr]
```

Thus, in an unparenthesized sequence of power and unary operators, the operators are **evaluated from right to left** (this does not constrain the evaluation order for the operands): -1**2 results in -1.

The power operator has the same semantics as the built-in ``pow()`` function, when called with two arguments: it yields its left argument raised to the power of its right argument. The numeric arguments are first converted to a common type, and the result is of that type.

For ``int`` operands, the result has the same type as the operands unless the second argument is negative; in that case, all arguments are converted to float and a float result is delivered. For example, 10\*\*2 returns 100, but 10\*\*-2 returns 0.01.

Raising 0.0 to a negative power results in a ``ZeroDivisionError``. Raising a negative number to a fractional power results in a complex number.

## <a name="6_6"></a> 6.6. Unary arithmetic and bitwise operations

All unary arithmetic and bitwise operations have the same priority:

```
u_expr ::=  power | "-" u_expr | "+" u_expr | "~" u_expr
```

The unary - (minus) operator yields the negation of its numeric argument. The unary + (plus) operator yields its numeric argument unchanged. The unary ~ (invert) operator yields the bitwise inversion of its integer argument. The bitwise inversion of ``x`` is defined as ``-(x+1)``. It only applies to integral numbers.

In all three cases, if the argument does not have the proper type, a ``TypeError`` exception is raised.

## <a name="6_7"></a> 6.7. Binary arithmetic operations

The binary arithmetic operations have the conventional priority levels. Note that some of these operations also apply to certain non-numeric types. Apart from the power operator, there are only two levels, one for multiplicative operators and one for additive operators:

```
m_expr ::=  u_expr | m_expr "*" u_expr | m_expr "@" m_expr |
            m_expr "//" u_expr | m_expr "/" u_expr |
            m_expr "%" u_expr
a_expr ::=  m_expr | a_expr "+" m_expr | a_expr "-" m_expr
```

The ``*`` (multiplication) operator yields the product of its arguments. The arguments must either both be numbers, or one argument must be an integer and the other must be a sequence. In the former case, the numbers are converted to a common type and then multiplied together. In the latter case, sequence repetition is performed; a negative repetition factor yields an empty sequence.

The ``@`` (at) operator is intended to be used for matrix multiplication. **No builtin Python types implement this operator**.

The ``/`` (division) and ``//`` (floor division) operators yield the quotient of their arguments. The numeric arguments are first converted to a common type. Division of integers yields a float, while floor division of integers results in an integer; the result is that of mathematical division with the ‘floor’ function applied to the result. Division by zero raises the ``ZeroDivisionError`` exception.

The ``%`` (modulo) operator yields the remainder from the division of the first argument by the second. The numeric arguments are first converted to a common type. A zero right argument raises the ``ZeroDivisionError`` exception. The arguments may be floating point numbers, e.g., 3.14%0.7 equals 0.34 (since 3.14 equals 4*0.7 + 0.34.) The modulo operator always yields a result with the same sign as its second operand (or zero); the absolute value of the result is strictly smaller than the absolute value of the second operand.

The floor division and modulo operators are connected by the following identity: ``x == (x//y)*y + (x%y)``. Floor division and modulo are also connected with the built-in function ``divmod()``:`` divmod(x, y) == (x//y, x%y)``.

The floor division operator, the modulo operator, and the ``divmod()`` function are not defined for complex numbers. Instead, convert to a floating point number using the ``abs()`` function if appropriate.

The + (addition) operator yields the sum of its arguments. The arguments must either both be numbers or both be sequences of the same type. In the former case, the numbers are converted to a common type and then added together. In the latter case, the sequences are concatenated. The - (subtraction) operator yields the difference of its arguments. The numeric arguments are first converted to a common type.

## <a name="6_8"></a> 6.8. Shifting operations

The shifting operations have lower priority than the arithmetic operations:

```
shift_expr ::=  a_expr | shift_expr ("<<" | ">>") a_expr
```

These operators accept integers as arguments. They shift the first argument to the left or right by the number of bits given by the second argument.

A _right shift_ by n bits is defined as **floor division** by ``pow(2,n)``. A _left shift_ by n bits is defined as **multiplication** with ``pow(2,n)``.

## <a name="6_9"></a> 6.9. Binary bitwise operations

Each of the three bitwise operations has a different priority level:

```
and_expr ::=  shift_expr | and_expr "&" shift_expr
xor_expr ::=  and_expr | xor_expr "^" and_expr
or_expr  ::=  xor_expr | or_expr "|" xor_expr
```

The ``&`` operator yields the bitwise AND of its arguments, _which must be integers_.

The ``^`` operator yields the bitwise XOR (exclusive OR) of its arguments, _which must be integers_.

The ``|`` operator yields the bitwise (inclusive) OR of its arguments, _which must be integers_.
