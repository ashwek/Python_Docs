[Go Back](./README.md)

# <a name="2"></a> 2. Using the Python Interpreter

## <a name="2_1"></a> 2.1. Invoking the Interpreter

The Python interpreter is usually installed as **/usr/local/bin/python3.7**

### <a name="2_1_1"></a> 2.1.1. Argument Passing

When known to the interpreter, the **script name & additional command line arguments** are turned into a _list of strings_ & assigned to the ```argv``` variable in the ```sys``` _module_. When -c command is used, sys.argv[0] is set to '-c'. When -m module is used, sys.argv[0] is set to the full name of the located module.

## <a name="2_2"></a> 2.2. The Interpreter and Its Environment

### <a name="2_2_1"></a> 2.2.1. Source Code Encoding

By default, Python source files are treated as _encoded in UTF-8_. To declare an encoding other than the default one, a special comment line should be added as the first line of the file. The syntax is as follows:

```python3
# -*- coding: encoding -*-
```

where _encoding_ is one of the valid codecs supported by Python. One exception to the first line rule is when the source code starts with a UNIX “shebang” line. In this case, the encoding declaration should be added as the second line of the file. For example:

```python3
#!/usr/bin/env python3
# -*- coding: encoding -*-
```
