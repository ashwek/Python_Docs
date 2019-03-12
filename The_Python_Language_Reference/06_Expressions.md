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
