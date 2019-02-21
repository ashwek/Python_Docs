[Go Back](./README.md)

# <a name="5"></a> 5. Data Structures

## <a name="5_1"></a> 5.1. More on Lists

```python3

list.append(x)
# Add item to the end of the list. Equivalent to a[len(a):] = [x].

list.extend(iterable)
# Equivalent to a[len(a):] = iterable.

list.insert(i, x)
# a.insert(len(a)+x, x) is equivalent to a.append(x).

list.remove(x)
# Removes first occurence of x. It raises a ValueError if there is no such item.

list.pop([i])

list.clear()
# Equivalent to del a[:].

list.index(x[, start[, end]])
# Return index of first occurence of x. Raises a ValueError if there is no such item.

list.count(x)

list.sort(key=None, reverse=False)

list.reverse()

list.copy()
# Return a shallow copy of the list. Equivalent to a[:].

```

### <a name="5_1_3"></a> 5.1.3. List Comprehensions

## <a name="5_2"></a> 5.2. The del statement

This differs from the ```pop()``` method which returns a value. The ```del``` statement can also be used to remove slices from a list or clear the entire list. For example:

```python3
>>> a = [-1, 1, 66.25, 333, 333, 1234.5]
>>> del a[0]
>>> a
[1, 66.25, 333, 333, 1234.5]
>>> del a[2:4]
>>> a
[1, 66.25, 1234.5]
>>> del a[:]
>>> a
[]

# del can also be used to delete entire variables:
>>> del a
```

Referencing the name a hereafter is an error _(at least until another value is assigned to it)_.

## <a name="5_3"></a> 5.3. Tuples and Sequences

Though ```tuples``` may seem similar to ```lists```, they are often used in different situations and for different purposes. Tuples are **immutable**, and usually contain a heterogeneous sequence of elements that are accessed via unpacking or indexing (or even by attribute in the case of _namedtuples_). Lists are mutable, and their elements are usually homogeneous and are accessed by iterating over the list.

The statement ```t = 12345, 54321, 'hello!'``` is an example of **tuple packing**: the values ```12345```, ```54321``` and ```'hello!'``` are packed together in a tuple. The reverse operation is also possible: ```x, y, z = t``` This is called **sequence unpacking** and works for any sequence on the right-hand side.

## <a name="5_4"></a> 5.4. Sets

A ```set``` is an **unordered collection of unique elements**. Set objects also support mathematical operations like ```union```, ```intersection```, ```difference```, and ```symmetric_difference``` .

``{}`` or the ```set()``` function can be used to create sets. Note: to **create an empty set** you have to use **set()**, not {}.

```python3
 # set comprehensions
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```

## <a name="5_5"></a> 5.5. Dictionaries

Unlike sequences, which are indexed by a range of numbers, **dictionaries are indexed by keys**, which can be any _**immutable type**_; strings and numbers can always be keys.

It is best to think of a dictionary as a _set of key: value pairs_, with the requirement that the **keys are unique**. A pair of braces creates an _empty dictionary_: ```{}```.

It is also possible to delete a key:value pair with ```del```. If you store using a key that is already in use, the _old value associated with that key is forgotten_. It is an _error to extract_ a value using a _non-existent key_.

Performing ```list(d)``` on a dictionary returns a **list of all the _keys_** used in the dictionary, in _**insertion order**_.

```python3
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'guido': 4127, 'jack': 4098}

# dict comprehensions
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}

# When the keys are simple strings, it is sometimes easier to specify pairs using keyword arguments:
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'guido': 4127, 'jack': 4098}
```

## <a name="5_6"></a> 5.6. Looping Techniques

```python3
# iterating through dictionary
for k, v in c.items():
    print(k, v)

# index & corresponding value can be retrieved at the same time using enumerate()
for i, v in enumerate(['tic', 'tac', 'toe']):
    print(i, v)

# 2 / more sequences can be paired with the zip() function
>>> questions = ['name', 'quest', 'favorite color']
>>> answers = ['lancelot', 'the holy grail', 'blue']
>>> for q, a in zip(questions, answers):
    print('What is your {0}?  It is {1}.'.format(q, a))
What is your name?  It is lancelot.
What is your quest?  It is the holy grail.
What is your favorite color?  It is blue.
```

## <a name="5_7"></a> 5.7. More on Conditions

The comparison operators ```in``` and ```not in``` check whether a value occurs (does not occur) in a sequence. The operators ```is``` and ```is not``` compare whether two objects are really **the same object**. _All comparison operators have the same priority_, which is lower than that of all numerical operators.

The Boolean operators ```and``` and ```or``` are so-called **short-circuit operators**: their arguments are evaluated from left to right, and **evaluation stops as soon as the outcome is determined**. For example, if A and C are true but B is false, A and B and C does not evaluate the expression C. When used as a general value and not as a Boolean, the return value of a short-circuit operator is the last evaluated argument.

Note that in Python, unlike C, **assignment cannot occur inside expressions**

## <a name="5_8"></a> 5.8. Comparing Sequences and Other Types

Sequence objects may be compared to other objects with the same sequence type. The comparison uses **lexicographical ordering**: first the first two items are compared, and if they differ this determines the outcome of the comparison; if they are equal, the next two items are compared, and so on, until either sequence is exhausted. If two items to be compared are themselves sequences of the same type, the lexicographical comparison is carried out recursively. If all items of two sequences compare equal, the sequences are considered equal. If one sequence is an initial sub-sequence of the other, the shorter sequence is the smaller (lesser) one. Lexicographical ordering for strings uses the Unicode code point number to order individual characters.
