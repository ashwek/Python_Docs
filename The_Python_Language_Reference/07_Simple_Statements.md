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
