[Go Back](./README.md)


# <a name="9"></a> 9. Top-Level Components

The Python interpreter can get its input from a number of sources: from a script passed to it as standard input or as program argument, typed in interactively, from a module source file, etc.

## <a name="9_2"></a> 9.2. File input

All input read from non-interactive files has the same form:

```
file_input ::=  (NEWLINE | statement)*
```

This syntax is used in the following situations:
 - when parsing a complete Python program (from a file or from a string);
 - when parsing a module;
 - when parsing a string passed to the exec() function;

## <a name="9_3"></a> 9.3. Interactive input

Input in interactive mode is parsed using the following grammar:

```
interactive_input ::=  [stmt_list] NEWLINE | compound_stmt NEWLINE
```

Note that a (top-level) compound statement must be followed by a blank line in interactive mode; this is needed to help the parser detect the end of the input.

## <a name="9_4"></a> 9.4. Expression input

``eval()`` is used for expression input. It ignores leading whitespace. The string argument to ``eval()`` must have the following form:

```
eval_input ::=  expression_list NEWLINE*
```
