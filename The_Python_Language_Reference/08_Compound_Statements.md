[Go Back](./README.md)

# <a name="8"></a> 8. Compound Statements

Compound statements contain (groups of) other statements; they affect or control the execution of those other statements in some way. In general, compound statements span multiple lines, although in simple incarnations a whole compound statement may be contained in one line.

The ``if``, ``while`` and ``for`` statements implement traditional control flow constructs. ``try`` specifies exception handlers and/or cleanup code for a group of statements, while the ``with`` statement allows the execution of initialization and finalization code around a block of code. Function and class definitions are also syntactically compound statements.

A compound statement consists of one or more _clauses_. A _clause_ consists of a header and a _suite_. The _clause_ headers of a particular compound statement are all at the same indentation level. **Each clause header begins with a uniquely identifying keyword and ends with a colon.** A suite is a group of statements controlled by a clause.** A suite can be one or more semicolon-separated simple statements on the same line as the header, following the header’s colon, or it can be one or more indented statements on subsequent lines.** Only the latter form of a suite can contain nested compound statements; the following is illegal, mostly because it wouldn’t be clear to which if clause a following else clause would belong:

```python
if test1: if test2: print(x)
```

Also note that the **semicolon binds tighter than the colon** in this context, so that in the following example, either all or none of the ``print()`` calls are executed:

```python
if x < y < z: print(x); print(y); print(z)
```

## <a name="8_1"></a> 8.1. The if statement

The ``if`` statement is used for conditional execution:

```
if_stmt ::=  "if" expression ":" suite
             ("elif" expression ":" suite)*
             ["else" ":" suite]
```

It selects exactly one of the suites by evaluating the expressions one by one until one is found to be true; then that suite is executed. If all expressions are false, the suite of the else clause, if present, is executed.

## <a name="8_2"></a> 8.2. The while statement

The ``while`` statement is used for repeated execution as long as an expression is true:

```
while_stmt ::=  "while" expression ":" suite
                ["else" ":" suite]
```

This repeatedly tests the expression and, if it is true, executes the first suite; if the expression is false the suite of the else clause, if present, is executed and the loop terminates.

A ``break`` statement executed in the first suite terminates the loop without executing the else clause’s suite. A ``continue`` statement executed in the first suite skips the rest of the suite and goes back to testing the expression.

## <a name="8_3"></a> 8.3. The for statement

The ``for`` statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable object:

```
for_stmt ::=  "for" target_list "in" expression_list ":" suite
              ["else" ":" suite]
```

The expression list is evaluated once; it should yield an iterable object. An iterator is created for the result of the expression_list. The suite is then executed once for each item provided by the iterator, in the order returned by the iterator. Each item in turn is assigned to the target list using the standard rules for assignments, and then the suite is executed. When the items are exhausted, the suite in the ``else`` clause, if present, is executed, and the loop terminates.

## <a name="8_4"></a> 8.4. The try statements

The ``try`` statement specifies exception handlers and/or cleanup code for a group of statements:

```
try_stmt  ::=  try1_stmt | try2_stmt
try1_stmt ::=  "try" ":" suite
               ("except" [expression ["as" identifier]] ":" suite)+
               ["else" ":" suite]
               ["finally" ":" suite]
try2_stmt ::=  "try" ":" suite
               "finally" ":" suite
```

The ``except`` clause(s) specify one or more exception handlers. When an exception occurs in the ``try`` suite, a search for an exception handler is started. This search inspects the except clauses in turn until one is found that matches the exception. An expression-less except clause, must be last; it matches any exception.

For an except clause with an expression, that expression is evaluated, and the clause matches the exception if the resulting object is “compatible” with the exception. An object is compatible with an exception if it is the class or a base class of the exception object or a tuple containing an item compatible with the exception.

When a matching except clause is found, the exception is assigned to the target specified after the ``as`` keyword in that ``except`` clause, if present, and the except clause’s suite is executed. When an exception has been assigned using ``as`` target, it is _cleared at the end of the except clause_. This is as if

```python
except E as N:
    foo
```

was translated to

```python
except E as N:
    try:
        foo
    finally:
        del N
```

Exceptions are cleared because with the traceback attached to them, they form a reference cycle with the stack frame, keeping all locals in that frame alive until the next garbage collection occurs.

Before an except clause’s suite is executed, details about the exception are stored in the ``sys`` module and can be accessed via ``sys.exc_info()``. ``sys.exc_info()`` returns a 3-tuple consisting of the exception class, the exception instance and a traceback object identifying the point in the program where the exception occurred. ``sys.exc_info()`` values are restored to their previous values when returning from a function that handled an exception.

The optional ``else`` clause is executed if the control flow leaves the ``try`` suite, no exception was raised, and no ``return``, ``continue``, or ``break`` statement was executed. **Exceptions in the ``else`` clause are not handled by the preceding ``except`` clauses.**

## <a name="8_5"></a> 8.5. The with statement

The ``with`` statement is used to _wrap the execution of a block with methods defined by a context manager_ ([With Statement Context Managers](./03_Data_Model.md#3_3_9)). This allows common try…except…finally usage patterns to be encapsulated for convenient reuse.

```
with_stmt ::=  "with" with_item ("," with_item)* ":" suite
with_item ::=  expression ["as" target]
```

The execution of the ``with`` statement with one “item” proceeds as follows:
  1. The context expression (the expression given in the with_item) is evaluated to obtain a context manager.
  2. The context manager’s ``__exit__()`` is loaded for later use.
  3. The context manager’s ``__enter__()`` method is invoked.
  4. If a target was included in the ``with`` statement, the return value from ``__enter__()`` is assigned to it.
  5. The suite is executed.
  6. The context manager’s ``__exit__()`` method is invoked. If an exception caused the suite to be exited, its type, value, and traceback are passed as arguments to ``__exit__()``. Otherwise, three ``None`` arguments are supplied.

With more than one item, the context managers are processed as if multiple ``with`` statements were nested:

```python
with A() as a, B() as b:
    suite
```

is equivalent to

```python
with A() as a:
    with B() as b:
        suite
```

## <a name="8_6"></a> 8.6. Function definitions

A function definition defines a user-defined function object:

```
funcdef                 ::=  [decorators] "def" funcname "(" [parameter_list] ")"
                             ["->" expression] ":" suite
decorators              ::=  decorator+
decorator               ::=  "@" dotted_name ["(" [argument_list [","]] ")"] NEWLINE
dotted_name             ::=  identifier ("." identifier)*
parameter_list          ::=  defparameter ("," defparameter)* ["," [parameter_list_starargs]]
                             | parameter_list_starargs
parameter_list_starargs ::=  "*" [parameter] ("," defparameter)* ["," ["**" parameter [","]]]
                             | "**" parameter [","]
parameter               ::=  identifier [":" expression]
defparameter            ::=  parameter ["=" expression]
funcname                ::=  identifier
```

A function definition is an executable statement. Its execution binds the function name in the current local namespace to a function object. This function object contains a reference to the current global namespace as the global namespace to be used when the function is called.

A function definition may be wrapped by one or more _decorator_ expressions. Decorator expressions are evaluated when the function is defined, in the scope that contains the function definition. The **result must be a callable**, which is invoked with the function object as the only argument. _The returned value is bound to the function name instead of the function object._ Multiple decorators are applied in nested fashion. For example, the following code

```python
@f1(arg)
@f2
def func(): pass
```

is roughly equivalent to

```python
def func(): pass
func = f1(arg)(f2(func))
```

except that the original function is not temporarily bound to the name ``func``.

_Default parameter values are evaluated from left to right when the function definition is executed_. This means that **the expression is evaluated once, when the function is defined, and that the same “pre-computed” value is used for each call**. This is especially important to understand when a default parameter is a mutable object, such as a list or a dictionary: if the function modifies the object, the default value is in effect modified. This is generally not what was intended. A way around this is to use ``None`` as the default, and explicitly test for it in the body of the function.

Functions are _first-class objects_. A ``def`` statement executed inside a function definition defines a local function that can be returned or passed around.

## <a name="8_7"></a> 8.7. Class definitions

A class definition defines a class object:

```
classdef    ::=  [decorators] "class" classname [inheritance] ":" suite
inheritance ::=  "(" [argument_list] ")"
classname   ::=  identifier
```

A class definition is an executable statement. The inheritance list usually gives a list of base classes, so each item in the list should evaluate to a class object which allows subclassing. Classes without an inheritance list inherit, by default, from the base class ``object``.

Variables defined in the class definition are class attributes; they are shared by instances. Instance attributes can be set in a method with ``self.name = value``. Both class and instance attributes are accessible through the notation ``self.name``, and an instance attribute hides a class attribute with the same name when accessed in this way. Class attributes can be used as defaults for instance attributes.

## <a name="8_8"></a> 8.8. Coroutines

### <a name="8_8_1"></a> 8.8.1. Coroutine function definition
