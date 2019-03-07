[Go Back](./README.md)

# <a name="4"></a> 4. Execution Model

## <a name="4_1"></a> 4.1. Structure of a program

A Python program is constructed from _code blocks_. A _block_ is a piece of Python program text that is executed as a unit. The following are blocks: a module, a function body, and a class definition, commands typed interactively, a script file (given as standard input to the interpreter), string argument passed to the built-in functions ``eval()`` and ``exec()``.

A code block is executed in an **execution frame**. A frame contains some administrative information (used for debugging) and determines where and how execution continues after the code block’s execution has completed.

## <a name="4_2"></a> 4.2. Naming and binding

### <a name="4_2_1"></a> 4.2.1. Binding of names

_Names refer to objects_. Names are introduced by _name binding operations_.

The following constructs bind names:
- formal parameters to functions
- import statements
- class and function definitions
- targets that are identifiers if occurring in an assignment
- for loop header
- after ``as`` in a ``with`` statement or ``except`` clause
- The ``import`` statement of the form ``from ... import *`` binds all names defined in the imported module, _except those beginning with an underscore_.

Each assignment or import statement occurs within a block defined by a class or function definition or at the module level (the top-level code block).

### <a name="4_2_2"></a> 4.2.2. Resolution of names

A **scope** defines the _visibility_ of a name within a block. If a local variable is defined in a block, its scope includes that block. If the definition occurs in a function block, the scope extends to any blocks contained within the defining one, unless a contained block introduces a different binding for the name. When a name is used in a code block, it is resolved using the _nearest enclosing scope_. The set of all such scopes visible to a code block is called the block’s environment.

When a name is not found at all, a ``NameError`` exception is raised. If the current scope is a function scope, and the name refers to a local variable that has not yet been bound to a value at the point where the name is used, an ``UnboundLocalError`` exception is raised. ``UnboundLocalError`` is a subclass of ``NameError``.

If the ``global`` statement occurs within a block, all uses of the name specified in the statement refer to the binding of that name in the top-level namespace. The global namespace is searched first. If the name is not found there, the builtins namespace is searched. The global statement must precede all uses of the name. The ``global`` statement has the same scope as a name binding operation in the same block.

The ``nonlocal`` statement causes corresponding names to refer to previously bound variables in the nearest enclosing function scope. ``SyntaxError`` is raised at compile time if the given name does not exist in any enclosing function scope.

## <a name="4_3"></a> 4.3. Exceptions

The Python interpreter raises an exception when it detects a run-time error. A Python program can also explicitly raise an exception with the ``raise`` statement. Exception handlers are specified with the ``try … except`` statement. The ``finally`` clause of such a statement can be used to specify cleanup code which does not handle the exception, but is executed whether an exception occurred or not in the preceding code.

Python uses the “termination” model of error handling: an exception handler can find out what happened and continue execution at an outer level, but it cannot repair the cause of the error and retry the failing operation (except by re-entering the offending piece of code from the top).

_Exceptions are identified by class instances._ The ``except`` clause is selected depending on the class of the instance: it must reference the class of the instance or a base class thereof. The instance can be received by the handler and can carry additional information about the exceptional condition.
