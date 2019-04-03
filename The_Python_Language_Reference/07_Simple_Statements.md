[Go Back](./README.md)

# <a name="7"></a> 7. Simple Statements

A simple statement is comprised within a _single logical line_. Several simple statements may occur on a single line _separated by semicolons_.

## <a name="7_1"></a> 7.1. Expression statements

Expression statements are used to compute and write a value, or to call a procedure. An expression statement evaluates the expression list (which may be a single expression).

In interactive mode, if the value is not ``None``, it is converted to a string using the built-in ``repr()`` function and the resulting string is written to standard output on a line by itself.

## <a name="7_2"></a> 7.2. Assignment Statements

Assignment statements are used to _(re)bind_ names to values and to modify attributes or items of mutable objects.

An assignment statement evaluates the expression list (remember that this can be a single expression or a comma-separated list, the latter yielding a tuple) and assigns the single resulting object to each of the target lists, from left to right.

### <a name="7_2_1"></a> 7.2.1. Augmented Assignment statements

Augmented assignment is the combination, in a single statement, of a _binary operation and an assignment statement_.

``+= -= *= @= /= //= %= **= >>= <<= &= ^= |=``

An augmented assignment evaluates the target (which, unlike normal assignment statements, cannot be an unpacking) and the expression list, performs the binary operation specific to the type of assignment on the two operands, and **assigns the result to the original target**. The target is only evaluated once.

An augmented assignment expression like ``x += 1`` can be rewritten as ``x = x + 1`` to achieve a _similar, but not exactly equal_ effect. In the augmented version, ``x`` is only evaluated once. Also, **when possible, the actual operation is performed in-place**.

Unlike normal assignments, augmented assignments _evaluate the left-hand side before evaluating the right-hand side_. For example, ``a[i] += f(x)`` first looks-up ``a[i]``, then it evaluates ``f(x)`` and performs the addition, and lastly, it writes the result back to ``a[i]``.

## <a name="7_3"></a> 7.3. The  assert statement

Assert statements are a convenient way to _insert debugging assertions_ into a program.
```python3
#assert_stmt ::=  "assert" expression ["," expression]

#The simple form, assert expression, is equivalent to

if __debug__:
    if not expression: raise AssertionError

#The extended form, assert expression1, expression2, is equivalent to

if __debug__:
    if not expression1: raise AssertionError(expression2)
```

These equivalences assume that ``__debug__`` and ``AssertionError`` refer to the built-in variables with those names.

## <a name="7_4"></a> 7.4. The pass statement

``pass`` is a **null operation** — when it is executed, nothing happens. It is useful as a placeholder when a statement is required syntactically, but no code needs to be executed.

## <a name="7_5"></a> 7.5. The del statement

```
del_stmt ::=  "del" target_list
```

Deletion is _recursively defined_ very similar to the way assignment is defined.

Deletion of a target list recursively deletes each target, from left to right. Deletion of a name removes the binding of that name from the local or global namespace, depending on whether the name occurs in a global statement in the same code block. If the name is unbound, a ``NameError`` exception will be raised. Deletion of attribute references, subscriptions and slicings is passed to the primary object involved.

## <a name="7_6"></a> 7.6. The return statement

``return`` may only occur syntactically nested in a function definition, not within a nested class definition.

If an expression list is present, it is evaluated, else ``None`` is substituted.

When return passes control out of a ``try`` statement with a ``finally`` clause, that ``finally`` clause is executed before really leaving the function.

In a generator function, the ``return`` statement indicates that the generator is done and will cause ``StopIteration`` to be raised. The returned value (if any) is used as an argument to construct ``StopIteration`` and becomes the ``StopIteration.value`` attribute.

## <a name="7_7"></a> 7.7. The yield statement

Yield expressions and statements are only used when defining a generator function, and are only used in the body of the generator function. Using ``yield`` in a function definition is sufficient to cause that definition to create a generator function instead of a normal function.

## <a name="7_8"></a> 7.8. The raise statement

If no expressions are present, ``raise`` re-raises the last exception that was active in the current scope. If no exception is active in the current scope, a ``RuntimeError`` exception is raised indicating that this is an error.

The ``from`` clause is used for **exception chaining**: if given, the second expression must be another exception class or instance, which will then be attached to the raised exception as the ``__cause__`` attribute. If the raised exception is not handled, both exceptions will be printed:

```python3
>>> try:
...     print(1 / 0)
... except Exception as exc:
...     raise RuntimeError("Something bad happened") from exc
...
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
RuntimeError: Something bad happened
```

## <a name="7_9"></a> 7.9. The break statement

``break`` may only occur syntactically nested in a ``for`` or ``while`` loop, but not nested in a function or class definition within that loop. It terminates the nearest enclosing loop, skipping the optional ``else`` clause if the loop has one.

If a ``for`` loop is terminated by ``break``, the loop control target keeps its current value. When ``break`` passes control out of a ``try`` statement with a ``finally`` clause, that ``finally`` clause is executed before really leaving the loop.

## <a name="7_10"></a> 7.10. The continue statement

``continue`` may only occur syntactically nested in a ``for`` or ``while`` loop, but not nested in a function or class definition or finally clause within that loop. It continues with the next cycle of the nearest enclosing loop.

When continue passes control out of a ``try`` statement with a ``finally`` clause, that ``finally`` clause is executed before really starting the next loop cycle.

## <a name="7_11"></a> 7.11. The import statement

The basic ``import`` statement (no ``from`` clause) is executed in two steps:
 - find a module, loading and initializing it if necessary
 - define a name or names in the local namespace for the scope where the import statement occurs.

When the statement contains multiple clauses (separated by commas) the two steps are carried out separately for each clause, just as though the clauses had been separated out into individual import statements.

If the requested module is retrieved successfully, it will be made available in the local namespace in one of three ways:
 - If the module name is followed by ``as``, then the name following ``as`` is bound directly to the imported module.
 - If no other name is specified, and the module being imported is a top level module, the module’s name is bound in the local namespace as a reference to the imported module
 - If the module being imported is not a top level module, then the name of the top level package that contains the module is bound in the local namespace as a reference to the top level package. The imported module must be accessed using its full qualified name rather than directly

The ``from`` form uses a slightly more complex process:
 - find the module specified in the ``from`` clause, loading and initializing it if necessary;
 - for each of the identifiers specified in the import clauses:
  - check if the imported module has an attribute by that name
  - if not, attempt to import a submodule with that name and then check the imported module again for that attribute
  - if the attribute is not found, ``ImportError`` is raised.
  - otherwise, a reference to that value is stored in the local namespace, using the name in the as clause if it is present, otherwise using the attribute name

The _public names_ defined by a module are determined by checking the module’s namespace for a variable named ``__all__``; if defined, it must be a sequence of _strings which are names defined or imported by that module_. The names given in ``__all__`` are all considered public and are required to exist. If ``__all__`` is not defined, the set of public names includes all names found in the module’s namespace which do not begin with an underscore character ('\_').

When specifying what module to import you do not have to specify the absolute name of the module. When a module or package is contained within another package it is possible to make a **relative import** within the same top package without having to mention the package name. _By using leading dots_ in the specified module or package after from you can specify how high to traverse up the current package hierarchy without specifying exact names. _1 leading dot means the current package_ where the module making the import exists. _2 dots means up 1 package level_. _3 dots is up 2 levels_, etc. So if you execute ``from . import mod`` from a module in the ``pkg`` package then you will end up importing ``pkg.mod``. If you execute ``from ..subpkg2 import mod`` from within ``pkg.subpkg1`` you will import ``pkg.subpkg2.mod``.

### <a name="7_11_1"></a> 7.11.1. Future Statements

A future statement is a _directive_ to the compiler that a particular _module should be compiled **using syntax or semantics** that will be **available in a specified future release of Python**_ where the feature becomes standard.

A future statement is recognized and _treated specially at compile time_: Changes to the semantics of core constructs are often implemented by generating different code. It may even be the case that a new feature introduces new incompatible syntax (such as a new reserved word), in which case the compiler may need to parse the module differently. Such decisions cannot be pushed off until runtime.

The direct runtime semantics are the same as for any ``import`` statement: there is a standard module ``__future__``, described later, and it will be imported in the usual way at the time the future statement is executed. The interesting runtime semantics depend on the specific feature enabled by the future statement.

## <a name="7_12"></a> 7.12. The global statement

The ``global`` statement is a declaration which holds for the entire current code block. It means that the listed identifiers are to be interpreted as globals. It would be impossible to assign to a global variable without ``global``, although free variables may refer to globals without being declared global.

Names listed in a ``global`` statement must not be used in the same code block textually preceding that global statement.

``global`` is a directive to the parser. It applies only to code parsed at the same time as the global statement. In particular, a global statement contained in a string or code object supplied to the built-in ``exec()`` function does not affect the code block containing the function call, and code contained in such a string is unaffected by global statements in the code containing the function call. The same applies to the ``eval()`` and ``compile()`` functions.

## <a name="7_13"></a> 7.13. The nonlocal statement

The ``nonlocal`` statement causes the listed identifiers to _refer to previously bound variables in the nearest enclosing scope excluding globals_. This is important because the default behavior for binding is to search the local namespace first. The statement _allows encapsulated code to rebind variables outside of the local scope_ besides the global (module) scope.

Names listed in a ``nonlocal`` statement, unlike those listed in a global statement, must refer to pre-existing bindings in an enclosing scope. Names listed in a ``nonlocal`` statement must not collide with pre-existing bindings in the local scope.
