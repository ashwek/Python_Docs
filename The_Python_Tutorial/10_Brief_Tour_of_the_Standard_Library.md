[Go Back](./README.md)


# <a name="10"></a> 10. Brief Tour of the Standard Library

## <a name="10_1"></a> 10.1. Operating System Interface

The ``os`` module provides dozens of functions for interacting with the operating system:

```python
>>> import os
>>> os.getcwd()      # Return the current working directory
'C:\\Python37'
>>> os.chdir('/server/accesslogs')   # Change current working directory
>>> os.system('mkdir today')   # Run the command mkdir in the system shell
0
```

Be sure to use the ``import os`` style instead of ``from os import *``. This will keep ``os.open()`` from shadowing the built-in ``open()`` function which operates much differently.

The built-in ``dir()`` and ``help()`` functions are useful as interactive aids for working with large modules like ``os``:

```python
>>> import os
>>> dir(os)
#returns a list of all module functions
>>> help(os)
#returns an extensive manual page created from the module's docstrings
```

For daily file and directory management tasks, the ``shutil`` module provides a higher level interface that is easier to use:

```python
>>> import shutil
>>> shutil.copyfile('data.db', 'archive.db')
'archive.db'
>>> shutil.move('/build/executables', 'installdir')
'installdir'
```

## <a name="10_2"></a> 10.2. File Wildcards

The ``glob`` module provides a function for making file lists from directory wildcard searches:

```python
>>> import glob
>>> glob.glob('*.py')
['primes.py', 'random.py', 'quote.py']
```

## <a name="10_3"></a> 10.3. Command Line Arguments

Common utility scripts often need to process command line arguments. These arguments are stored in the ``sys`` module’s ``argv`` attribute as a list. For instance the following output results from running ``python demo.py one two three`` at the command line:

```python
>>> import sys
>>> print(sys.argv)
['demo.py', 'one', 'two', 'three']
```

The ``getopt`` module processes ``sys.argv`` using the conventions of the Unix ``getopt()`` function. More powerful and flexible command line processing is provided by the ``argparse`` module.

## <a name="10_4"></a> 10.4. Error Output Redirection and Program Termination

The ``sys`` module also has attributes for ``stdin``, ``stdout``, and ``stderr``. The most direct way to terminate a script is to use ``sys.exit()``.

## <a name="10_5"></a> 10.5. String Pattern Matching

The ``re`` module provides regular expression tools for advanced string processing. For complex matching and manipulation, regular expressions offer succinct, optimized solutions:

```python
>>> import re
>>> re.findall(r'\bf[a-z]*', 'which foot or hand fell fastest')
['foot', 'fell', 'fastest']
```

## <a name="10_6"></a> 10.6. Mathematics

The ``math`` module gives access to the underlying C library functions for floating point math:

```python
>>> import math
>>> math.cos(math.pi / 4)
0.70710678118654757
>>> math.log(1024, 2)
10.0
```

The ``random`` module provides tools for making random selections:

```python
>>> import random
>>> random.choice(['apple', 'pear', 'banana'])
'apple'
>>> random.sample(range(100), 10)   # sampling without replacement
[30, 83, 16, 4, 8, 81, 41, 50, 18, 33]
>>> random.random()    # random float
0.17970987693706186
>>> random.randrange(6)    # random integer chosen from range(6)
4
```

## <a name="10_7"></a> 10.7. Internet Access

There are a number of modules for accessing the internet and processing internet protocols. Two of the simplest are urllib.request for retrieving data from URLs and smtplib for sending mail:

```python
>>> from urllib.request import urlopen
>>> with urlopen('http://tycho.usno.navy.mil/cgi-bin/timer.pl') as response:
...     for line in response:
...         line = line.decode('utf-8')  # Decoding the binary data to text.
...         if 'EST' in line or 'EDT' in line:  # look for Eastern Time
...             print(line)

<BR>Nov. 25, 09:43:32 PM EST

>>> import smtplib
>>> server = smtplib.SMTP('localhost')
>>> server.sendmail('soothsayer@example.org', 'jcaesar@example.org',
... """To: jcaesar@example.org
... From: soothsayer@example.org
...
... Beware the Ides of March.
... """)
>>> server.quit()
```

(Note that the second example needs a mailserver running on localhost.)

## <a name="10_8"></a> 10.8. Dates and Times

The ``datetime`` module supplies classes for manipulating dates and times in both simple and complex ways. While date and time arithmetic is supported, the focus of the implementation is on efficient member extraction for output formatting and manipulation. The module also supports objects that are timezone aware.

```python
>>> # dates are easily constructed and formatted
>>> from datetime import date
>>> now = date.today()
>>> now
datetime.date(2003, 12, 2)
>>> now.strftime("%m-%d-%y. %d %b %Y is a %A on the %d day of %B.")
'12-02-03. 02 Dec 2003 is a Tuesday on the 02 day of December.'

>>> # dates support calendar arithmetic
>>> birthday = date(1964, 7, 31)
>>> age = now - birthday
>>> age.days
14368
```

## <a name="10_9"></a> 10.9. Data Compression

Common data archiving and compression formats are directly supported by modules including: ``zlib``, ``gzip``, ``bz2``, ``lzma``, ``zipfile`` and ``tarfile``.

## <a name="10_10"></a> 10.10. Performance Measurement

Some Python users develop a deep interest in knowing the relative performance of different approaches to the same problem. Python provides a measurement tool that answers those questions immediately.

```python
>>> from timeit import Timer
>>> Timer('t=a; a=b; b=t', 'a=1; b=2').timeit()
0.57535828626024577
>>> Timer('a,b = b,a', 'a=1; b=2').timeit()
0.54962537085770791
```

In contrast to timeit’s fine level of granularity, the ``profile`` and ``pstats`` modules provide tools for identifying time critical sections in larger blocks of code.

## <a name="10_11"></a> 10.11. Quality Control

One approach for developing high quality software is to write tests for each function as it is developed and to run those tests frequently during the development process.

The ``doctest`` module provides a tool for scanning a module and validating tests embedded in a program’s _docstrings_. Test construction is as simple as cutting-and-pasting a typical call along with its results into the docstring. This improves the documentation by providing the user with an example and it allows the ``doctest`` module to make sure the code remains true to the documentation:

```python
def average(values):
    """Computes the arithmetic mean of a list of numbers.

    >>> print(average([20, 30, 70]))
    40.0
    """
    return sum(values) / len(values)

import doctest
doctest.testmod()   # automatically validate the embedded tests
```

The ``unittest`` module is not as effortless as the ``doctest`` module, but it allows a more comprehensive set of tests to be maintained in a separate file:

```python
import unittest

class TestStatisticalFunctions(unittest.TestCase):

    def test_average(self):
        self.assertEqual(average([20, 30, 70]), 40.0)
        self.assertEqual(round(average([1, 5, 7]), 1), 4.3)
        with self.assertRaises(ZeroDivisionError):
            average([])
        with self.assertRaises(TypeError):
            average(20, 30, 70)

unittest.main()  # Calling from the command line invokes all tests
```
