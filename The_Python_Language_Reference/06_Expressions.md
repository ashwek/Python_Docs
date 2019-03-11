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
