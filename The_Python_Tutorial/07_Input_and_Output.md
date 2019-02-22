[Go Back](./README.md)

# <a name="7"></a> 7. Input and Output

## <a name="7_1"></a> 7.1. Fancier Output Formatting

Often we’ll want more control over the formatting of our output than simply printing space-separated values. There are several ways to format output.

- To use formatted string literals, begin a string with ```f``` or ```F``` before the opening quotation mark or triple quotation mark. Inside this string, you can write a Python expression between ```{``` and ```}``` characters that can refer to **variables** or **literal values**.
```python3
>>> year = 2016
>>> event = 'Referendum'
>>> f'Results of the {year} {event}'
'Results of the 2016 Referendum'
```

- The ```str.format()``` method of strings requires more manual effort. You’ll still use ```{``` and ```}``` to mark where a variable will be substituted and **can provide detailed formatting directives**, but you’ll also need to **provide the information to be formatted**.
```python3
>>> yes_votes = 42_572_654
>>> no_votes = 43_132_495
>>> percentage = yes_votes / (yes_votes + no_votes)
>>> '{:-9} YES votes  {:2.2%}'.format(yes_votes, percentage)
' 42572654 YES votes  49.67%'
```

### <a name="7_1_1"></a> 7.1.1. Formatted String Literals

**Formatted string literals** (also called **f-strings** for short) let you include the value of Python expressions inside a string by prefixing the string with ```f``` or ```F``` and writing expressions as ```{expression}```.

```python3
>>> import math
>>> print(f'The value of pi is approximately {math.pi:.3f}.')
The value of pi is approximately 3.142.
```

Passing an integer after the ```:``` will cause that field to be a **minimum number of characters wide**. This is useful for making columns line up.
```python3
>>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
>>> for name, phone in table.items():
...     print(f'{name:10} ==> {phone:10d}')
...
Sjoerd     ==>       4127
Jack       ==>       4098
Dcab       ==>       7678
```

Other **modifiers** can be used to **convert the value** before it is formatted. ```!a``` applies ```ascii()```, ```!s``` applies ```str()```, and ```!r``` applies ```repr()```:

```python3
>>> animals = 'eels'
>>> print(f'My hovercraft is full of {animals}.')
My hovercraft is full of eels.
>>> print(f'My hovercraft is full of {animals!r}.')
My hovercraft is full of 'eels'.
```

### <a name="7_1_2"></a> 7.1.2. The String format() Method

```python3
>>> print('We are the {} who say "{}!"'.format('knights', 'Ni'))
We are the knights who say "Ni!"

>>> print('{0} and {1}'.format('spam', 'eggs'))
spam and eggs
>>> print('{1} and {0}'.format('spam', 'eggs'))
eggs and spam

>>> print('This {food} is {adjective}.'.format(
...       food='spam', adjective='absolutely horrible'))
This spam is absolutely horrible.
```

The brackets and characters within them (called **format fields**) are replaced with the objects passed into the ```str.format()``` method. A number in the brackets can be used to refer to the _position_ of the object passed into the ```str.format()``` method. If keyword arguments are used in the ```str.format()``` method, their values are referred to by _using the name of the argument_.

### <a name="7_1_3"></a> 7.1.3. Manual String Formatting

```python3
>>> print("Abc".rjust(10), "Abc".ljust(10), "Abc".center(10), sep="\n")
       Abc
Abc       
   Abc    
```

The ```str.rjust()``` method of string objects **right-justifies** a string in a field of a given width by **padding it with spaces on the left**. There are similar methods ```str.ljust()``` and ```str.center()```. These methods do not write anything, they just return a new string. If the input string is too long, they don’t truncate it, but return it unchanged; this will mess up your column lay-out but that’s usually better than the alternative, which would be lying about a value.

## <a name="7_2"></a> 7.2. Reading and Writing Files

```python3
>>> f = open('workfile', 'w')
```

```open()``` returns a file object, and is most commonly used with two arguments: ```open(filename, mode)```.

The _first argument_ is a string containing the **filename**. The _second argument_ is another string containing a few characters describing the **way in which the file will be used (mode)**. Mode can be **r** for _reading_, **w** for _writing_, and **a** for _appending_. **r+** opens the file for both _reading and writing_. **r** is default mode.

**b** _appended to the mode_ opens the file in **binary mode**: now the data is read and written in the form of bytes objects.

### <a name="7_2_1"></a> 7.2.1. Methods of File Objects

To read a file’s contents, call ```f.read(size)```, which reads some quantity of data and returns it as a string or bytes object. ```size``` is an optional numeric argument. When ```size``` is omitted or negative, the **entire contents of the file will be read** and returned; it’s your problem if the file is twice as large as your machine’s memory. If the _end of the file_ has been reached, ```f.read()``` will return an empty string ('').

```python3
>>> f.read()
'This is the entire file.\n'
>>> f.read()
''
```

```f.readline()``` reads a single line from the file; a newline character (\n) is left at the end of the string, and is only omitted on the last line of the file if the file doesn’t end in a newline. This makes the return value unambiguous; if ```f.readline()``` returns an empty string, the end of the file has been reached, while a blank line is represented by '\n', a string containing only a single newline.

```python3
>>> f.readline()
'This is the first line of the file.\n'
>>> f.readline()
'Second line of the file\n'
>>> f.readline()
''
```

For reading lines from a file, you can loop over the file object. This is memory efficient, fast, and leads to simple code:

```python3
>>> for line in f:
...     print(line, end='')
...
This is the first line of the file.
Second line of the file
```

```f.write(string)``` writes the contents of string to the file, returning the number of characters written.

```python3
>>> f.write('This is a test\n')
15
```

```f.tell()``` returns an integer giving the **file object’s current position** in the file represented as number of bytes from the beginning of the file when in binary mode and an opaque number when in text mode.

To change the file object’s position, use ```f.seek(offset, from_what)```. The position is computed from adding _offset to a reference point_; the reference point is selected by the ```from_what``` argument. A ```from_what``` value of **0** measures from the _beginning of the file_, **1** uses the _current file position_, and **2** uses the _end of the file_ as the reference point. ```from_what``` can be omitted and defaults to 0, using the beginning of the file as the reference point.

### <a name="7_2_2"></a> 7.2.2. Saving structured data with json

Strings can easily be written to and read from a file. _Numbers take a bit more effort_, since the ```read()``` method only returns strings, which will have to be passed to a function like ```int()```, which takes a string like '123' and returns its numeric value 123. When you want to save more complex data types like nested lists and dictionaries, parsing and serializing by hand becomes complicated.

Python allows you to use the _popular data interchange format_ called **JSON (JavaScript Object Notation)**. The standard _module_ called ```json``` can take Python data hierarchies, and convert them to string representations; this process is called **serializing**. Reconstructing the data from the string representation is called **deserializing**. Between serializing and deserializing, the string representing the object may have been stored in a file or data, or sent over a network connection to some distant machine.


If you have an object x, you can view its JSON string representation with a simple line of code:

```python3
>>> import json
>>> json.dumps([1, 'simple', 'list'])
'[1, "simple", "list"]'

>>> a
{'5': 25, '4': 16, '3': 9}
>>> ja = json.dumps(a)
>>> ja
'{"5": 25, "4": 16, "3": 9}'
>>> json.loads(ja)
{'5': 25, '4': 16, '3': 9}
>>>

# With Files
>>> f = open("New.txt", "w")
>>> a
{'5': 25, '4': 16, '3': 9}
>>> json.dump(a, f)
>>> f.close()
>>>
>>> f = open("New.txt", "r")
>>> json.load(f)
{'5': 25, '4': 16, '3': 9}
```
