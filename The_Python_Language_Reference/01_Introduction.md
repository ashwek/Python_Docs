[Go Back](./README.md)

# <a name="1"></a> 1. Introduction

## <a name="1_1"></a> 1.1. Alternate Implementations

Though there is one Python implementation which is by far the most popular, there are some alternate implementations which are of particular interest to different audiences.

 - **CPython**

    This is the original and most-maintained implementation of Python, _written in C_. New language features generally appear here first.

 - **Jython**

    Python implemented in Java. This implementation _can be used as a scripting language for Java applications_, or can be used to create applications using the Java class libraries. It is also often used to create tests for Java libraries.

 - **Python for .NET**

    This implementation actually uses the CPython implementation, but is a managed .NET application and makes .NET libraries available.

 - **IronPython**

    An alternate Python for .NET. Unlike Python.NET, this is a complete Python implementation that generates IL, and compiles Python code directly to .NET assemblies.

- **PyPy**

    An implementation of Python written completely in Python. It supports several advanced features not found in other implementations like stackless support and a **Just in Time compiler**. One of the goals of the project is to encourage experimentation with the language itself by making it **easier to modify the interpreter**.

## <a name="1_2"></a> 1.2. Notation

The descriptions of lexical analysis and syntax use a **modified BNF grammar notation**. This uses the following style of definition:

```
name      ::=  lc_letter (lc_letter | "_")*
lc_letter ::=  "a"..."z"
```

The first line says that a _name_ is an *lc_letter* followed by a sequence of zero or more *lc_letters* and *underscores*. An *lc_letter* in turn is any of the single characters 'a' through 'z'.

Each rule begins with a _name_ and ::=.
A vertical bar (|) is used to separate alternatives. A star (*) means zero or more repetitions; a plus (+) means one or more repetitions, and a phrase enclosed in square brackets ([ ]) means zero or one occurrences. Literal strings are enclosed in quotes. White space is only meaningful to separate tokens.

In lexical definitions, two more conventions are used: Two literal characters separated by three dots mean a choice of any single character in the given (inclusive) range of ASCII characters. A phrase between angular brackets (<...>) gives an informal description of the symbol defined; e.g., this could be used to describe the notion of ‘control character’ if needed.

There is a big difference between the meaning of lexical and syntactic definitions: a **lexical definition** operates on the individual characters of the input source, while a **syntax definition** operates on the stream of tokens generated by the lexical analysis. All uses of BNF in the next chapter (“Lexical Analysis”) are lexical definitions; uses in subsequent chapters are syntactic definitions.