[Go Back](./README.md)

# <a name="11"></a> 11. Brief Tour of the Standard Library - Part II

This second tour covers more advanced modules that support professional programming needs. These modules rarely occur in small scripts.

## <a name="11_1"></a> 11.1. Output Formatting

The ``reprlib`` module provides a version of ``repr()`` customized for abbreviated displays of large or deeply nested containers.

The ``pprint`` module offers more sophisticated control over printing both built-in and user defined objects in a way that is readable by the interpreter. When the result is longer than one line, the “pretty printer” adds line breaks and indentation to more clearly reveal data structure.

The ``textwrap`` module formats paragraphs of text to fit a given screen width.

The ``locale`` module accesses a database of culture specific data formats. The grouping attribute of locale’s format function provides a direct way of formatting numbers with group separators.

## <a name="11_2"></a> 11.2. Templating

The ``string`` module includes a versatile Template class with a simplified syntax suitable for editing by end-users. This allows users to customize their applications without having to alter the application.

The format uses placeholder names formed by ``$`` with valid Python identifiers. Surrounding the placeholder with braces allows it to be followed by more alphanumeric letters with no intervening spaces. Writing ``$$`` creates a single escaped ``$``:

```python
>>> from string import Template
>>> t = Template('${village}folk send $$10 to $cause.')
>>> t.substitute(village='Nottingham', cause='the ditch fund')
'Nottinghamfolk send $10 to the ditch fund.'
```

## <a name="11_3"></a> 11.3. Working with Binary Data Record Layouts

The ``struct`` module provides ``pack()`` and ``unpack()`` functions for working with variable length binary record formats.

## <a name="11_4"></a> 11.4. Multi-threading

Threading is a technique for decoupling tasks which are not sequentially dependent. Threads can be used to improve the responsiveness of applications that accept user input while other tasks run in the background. A related use case is running I/O in parallel with computations in another thread.

The high level ``threading`` module can run tasks in background while the main program continues to run. The principal challenge of multi-threaded applications is coordinating threads that share data or other resources. To that end, the threading module provides a number of synchronization primitives including locks, events, condition variables, and semaphores.

## <a name="11_5"></a> 11.5. Logging

The ``logging`` module offers a full featured and flexible logging system. At its simplest, log messages are sent to a file or to ``sys.stderr``.

## <a name="11_6"></a> 11.6. Weak References

Python does automatic memory management. The memory is freed shortly after the last reference to it has been eliminated. This approach works fine for most applications but occasionally there is a need to track objects only as long as they are being used by something else. Unfortunately, just tracking them creates a reference that makes them permanent. The ``weakref`` module provides tools for _tracking objects without creating a reference_. When the object is no longer needed, it is automatically removed from a weakref table and a callback is triggered for weakref objects. _Typical applications include caching objects that are expensive to create_.

## <a name="11_7"></a> 11.7. Tools for Working with Lists

Many data structure needs can be met with the built-in list type. However, sometimes there is a need for alternative implementations with different performance trade-offs.

The ``array`` module provides an ``array()`` _object_ that is like a list that **stores only homogeneous data and stores it more compactly**. The following example shows an array of numbers stored as two byte unsigned binary numbers (typecode "H") rather than the usual _16 bytes per entry_ for regular lists of Python int objects.

```python
>>> from array import array
>>> a = array('H', [4000, 10, 700, 22222])
>>> sum(a)
26932
>>> a[1:3]
array('H', [10, 700])
```

The ``collections`` module provides a ``deque()`` object that is like a list with faster appends and pops from the left side but slower lookups in the middle. These objects are well suited for implementing queues and breadth first tree searches.

The ``heapq`` module provides functions for implementing heaps based on regular lists. The lowest valued entry is always kept at position zero. This is useful for applications which repeatedly access the smallest element but do not want to run a full list sort.

## <a name="11_8"></a> 11.8. Decimal Floating Point Arithmetic

The ``decimal`` module offers a ``Decimal`` datatype for decimal floating point arithmetic. Compared to the built-in ``float`` implementation of binary floating point, the class is especially helpful for
 - financial applications and other uses which require exact decimal representation,
 - control over precision,
 - control over rounding to meet legal or regulatory requirements,
 - tracking of significant decimal places, or
 - applications where the user expects the results to match calculations done by hand.

For example, calculating a 5% tax on a 70 cent phone charge gives different results in decimal floating point and binary floating point. The difference becomes significant if the results are rounded to the nearest cent:

```python
>>> from decimal import *
>>> round(Decimal('0.70') * Decimal('1.05'), 2)
Decimal('0.74')
>>> round(.70 * 1.05, 2)
0.73
```

The ``decimal`` module provides arithmetic with as much precision as needed:

```python
>>> getcontext().prec = 36
>>> Decimal(1) / Decimal(7)
Decimal('0.142857142857142857142857142857142857')
```
