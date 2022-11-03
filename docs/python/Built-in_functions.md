# Python Built-in Functions
The Python interpreter has a number of functions and types built into it that are always available.

| Function       | Description                                                        | 
|   :---:        |     :---:                                                          |
| abs()          | Return the absolute value of a number.                             | 
| aiter()        | Return an asynchronous iterator for an asynchronous iterable.      |
| all()          | Return True if all elements of the iterable are true.              |
| any()          | Return True if any element of the iterable is true.                |
| ascii()        | Return a string with a printable representation of an object.      |
| bin()          | Convert an integer number to a binary string.                      |
| bool()         | Return a Boolean value.                                            |
| breakpoint()   | Drops you into the debugger at the call site.                      |
| bytearray()    | Return a new array of bytes.                                       |
| bytes()        | Return a new “bytes” object.                                       |
| callable()     | Return True if the object argument is callable, False if not.      |
| chr()          | Return the string representing a character.                        |
| classmethod()  | Transform a method into a class method.                            |
| compile()      | Compile the source into a code or AST object.                      |
| complex()      | Return a complex number with the value real + imag*1j.             |
| delattr()      | Deletes the named attribute, provided the object allows it.        |
| dict()         | Create a new dictionary.                                           |
| dir()          | Return the list of names in the current local scope.               |
| divmod()       | Return a pair of numbers consisting of their quotient and remainder. |
| enumerate()    | Return an enumerate object.                                        |
| eval()         | Evaluates and executes an expression.                              |
| exec()         | This function supports dynamic execution of Python code.           |
| filter()       | Construct an iterator from an iterable and returns true.           |
| float()        | Return a floating point number from a number or string.            |
| format()       | Convert a value to a “formatted” representation.                   |
| frozenset()    | Return a new frozenset object.                                     |
| getattr()      | Return the value of the named attribute of object.                 |
| globals()      | Return the dictionary implementing the current module namespace.   |
| hasattr()      | True if the string is the name of one of the object’s attributes.  |
| hash()         | Return the hash value of the object.                               |
| help()         | Invoke the built-in help system.                                   |
| hex()          | Convert an integer number to a lowercase hexadecimal string.       |
| id()           | Return the “identity” of an object.                                |
| input()        | This function takes an input and converts it into a string.        |
| int()          | Return an integer object constructed from a number or string.      |
| isinstance()   | Return True if the object argument is an instance of an object.    |
| issubclass()   | Return True if class is a subclass of classinfo.                   |
| iter()         | Return an iterator object.                                         |
| len()          | Return the length (the number of items) of an object.              |
| list()         | Rather than being a function, list is a mutable sequence type.     |
| locals()       | Update and return a dictionary with the current local symbol table.|
| map()          | Return an iterator that applies function to every item of iterable.|
| max()          | Return the largest item in an iterable.                            |
| min()          | Return the smallest item in an iterable.                           |
| next()         | Retrieve the next item from the iterator.                          |
| object()       | Return a new featureless object.                                   |
| oct()          | Convert an integer number to an octal string.                      |
| open()         | Open file and return a corresponding file object.                  |
| ord()          | Return an integer representing the Unicode code point of a character. |
| pow()          | Return base to the power exp.                                      |
| print()        | Print objects to the text stream file.                             |
| property()     | Return a property attribute.                                       |
| repr()         | Return a string containing a printable representation of an object.|
| reversed()     | Return a reverse iterator.                                         |
| round()        | Return number rounded to ndigits precision after the decimal point.|
| set()          | Return a new set object.                                           |
| setattr()      | This is the counterpart of getattr().                              |
| slice()        | Return a sliced object representing a set of indices.              |
| sorted()       | Return a new sorted list from the items in iterable.               |
| staticmethod() | Transform a method into a static method.                           |
| str()          | Return a str version of object.                                    |
| sum()          | Sums start and the items of an iterable.                           |
| super()        | Return a proxy object that delegates method calls to a parent or sibling. |
| tuple()        | Rather than being a function, is actually an immutable sequence type. |
| type()         | Return the type of an object.                                      |
| vars()         | Return the dict attribute for any other object with a dict attribute. |
| zip()          | Iterate over several iterables in parallel.                        |
| import()       | This function is invoked by the import statement.                  |

# Examples:
abs()
```python
>>> abs(-1)
# 1
>>> abs(0)
# 0
>>> abs(1)
# 1
>>> abs(3.14)
# 3.14
>>> abs(3 + 2j)
# 3.6055512754639896
>>> abs(0x10)
# 16
>>> abs(0b10)
# 2
>>> abs(0o20)
# 16
```
***
aiter()
```python
>>> async def aitersync(iterable):
...    results = []
...    async for x in aiter(iterable):
...        results.append(x)
...    return iter(results)
```
***
all()
```python
>>> all([True, True, True])
# True

>>> all((0, True, False))
# False

>>> all({1, 1, 1})
# True
```
***
any()
```python
>>> any([False, False, False])
# False

>>> any((0, True, False))
# True

>>> any({0, 0, 0})
# False
```
***
ascii()
```python
>>> ascii('A')
# 'A'

>>> ascii('ë')
# '\xeb'

>>> ascii(['A', 'ë'])
# ['A', '\xeb']
```
***
bin()
```python
>>> bin(1)
# '0b1'

>>> bin(10)
# '0b1010'

>>> bin(100)
# '0b1100100'

>>> bin(1000)
# '0b1111101000'
```
***
bool()
```python
>>> bool(0)
# False

>>> bool(1)
# True

>>> bool(2)
# True

>>> bool('3')
# True

>>> bool(False)
# False

>>> bool(True)
# True
```
***
breakpoint()
```python
>>> # Create a loop over 5 integers
>>> for i in range(5):
...     # Stream i to stdout
...     print(i)
...    # Create breakpoint at # 3
...    if i == 3:
...        breakpoint()
...
# 0
# 1
# 2
# 3
# > c:\users\user\path\to\your\project\example.py(24)<module>()
# -> for i in range(5):
# (Pdb)
```
***
bytearray()
```python
>>> bytearray([1, 2, 3])
# bytearray(b'\x01\x02\x03')
```
***
bytes()
```python
>>> bytes(1)
# b'\x00'
>>> bytes(10)
# b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
>>> bytes(100)
# b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
...
```
***
callable()
```python
>>> def my_function():
...     print('this is a test function')
...
>>> callable(my_function)
# True
>>> callable(True)
# False
>>> callable(1)
# False
>>> callable('a')
# False
```
***
chr()
```python
>>> chr(1)
# '\x01'
>>> chr(10)
# '\n'
>>> chr(100)
# 'd'
>>> chr(1000)
# 'Ϩ'
```
***
complex()
```python
>>> complex(1)
# (1+0j)
>>> complex('1')
# (1+0j)
>>> complex(100)
# (100+0j)
>>> complex('100')
# (100+0j)
```
***
dict()
```python
>>> a = dict()
>>> type(a)
# <class 'dict'>
```
***
dir()
```python
>>> dir()
# ['__annotations__', '__builtins__', '__doc__', ...]

>>> dir(1)
# ['__abs__', '__add__', '__and__', '__bool__', ...]

>>> dir('a')
# ['__add__', '__class__', '__contains__', ...]
```
***
divmod()
```python
>>> divmod(2, 2)
# (1, 0)
>>> divmod(10, 2)
# (5, 0)
>>> divmod(7, 2)
# (3, 1)
```
***
enumerate()
```python
>>> l = enumerate([1, 2, 3, 4, 5])
>>> l
# <enumerate object at 0x7fcac409cc40>
>>> l.__next__()
# (0, 1)
>>> l.__next__()
# (1, 2)
>>> l.__next__()
# (2, 3)
>>> l.__next__()
# (3, 4)
>>> l.__next__()
# (4, 5)
>>> l.__next__()
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# StopIteration
```
`enumerate` is usually used in a `for` loop to get the index of an item:
```python
>>> for i, item in enumerate([1, 2, 3, 4, 5]):
...     print(f"Index: {i}, Item: {item}")
...
# Index: 0, Item: 1
# Index: 1, Item: 2
# Index: 2, Item: 3
# Index: 3, Item: 4
# Index: 4, Item: 5
```
***
eval()
```python
>>> eval('1 + 4')
# 5

>>> eval('print("Hello World!")')
# Hello World!

>>> x = 10
>>> eval('x == 10')
# True
```
***
float()
```python
>>> float('10')
# 10.0
>>> float(10)
# 10.0
```
***
frozenset()
```python
>>> frozenset([1, 2, 3])
# frozenset({1, 2, 3})

>>> frozenset({1, 2, 3})
# frozenset({1, 2, 3})

>>> frozenset((1, 2, 3))
# frozenset({1, 2, 3})
```
***
hash()
```python
>>> hash(1)
# 1
>>> hash('1')
# -3658718886659147670
>>> hash('10')
# 5216539490891759533
```
***
help()
```python
>>> help(type)
# Help on class type in module builtins:

# class type(object)
#  |  type(object_or_name, bases, dict)
#  |  type(object) -> the object's type
#  |  type(name, bases, dict) -> a new type
#  |
#  |  Methods defined here:
#  |
#  |  __call__(self, /, *args, **kwargs)
#  |      Call self as a function.
#  |
#  |  __delattr__(self, name, /)
#  |      Implement delattr(self, name).
#  |
#  |  __dir__(self, /)
#  |      Specialized __dir__ implementation for types.
#  |
#  |  __getattribute__(self, name, /)
# :

>>> help(str)
# Help on class str in module builtins:

# class str(object)
#  |  str(object='') -> str
#  |  str(bytes_or_buffer[, encoding[, errors]]) -> str
#  |
#  |  Create a new string object from the given object. If encoding or
#  |  errors is specified, then the object must expose a data buffer
#  |  that will be decoded using the given encoding and error handler.
#  |  Otherwise, returns the result of object.__str__() (if defined)
#  |  or repr(object).
#  |  encoding defaults to sys.getdefaultencoding().
#  |  errors defaults to 'strict'.
#  |
#  |  Methods defined here:
# :

>>> help(help)
# Help on _Helper in module _sitebuiltins object:

# class _Helper(builtins.object)
#  |  Define the builtin 'help'.
#  |
#  |  This is a wrapper around pydoc.help that provides a helpful message
#  |  when 'help' is typed at the Python interactive prompt.
#  |
#  |  Calling help() at the Python prompt starts an interactive help session.
#  |  Calling help(thing) prints help for the python object 'thing'.
#  |
#  |  Methods defined here:
#  |
#  |  __call__(self, *args, **kwds)
#  |      Call self as a function.
#  |
# :
```
***
hex()
```python
>>> hex(1)
# '0x1'
>>> hex(10)
# '0xa'
>>> hex(100)
# '0x64'
>>> hex(1000)
# '0x3e8'
```
***
id()
```python
>>> id(1)
# 9788960
>>> id('1')
# 140269689726000
>>> id([1, 2])
# 140269672924928
```
***
input()
This function takes the input from the user and converts it into a string:
```python
>>> print('What is your name?')   # ask for their name
>>> my_name = input()
>>> print('Hi, {}'.format(my_name))
# What is your name?
# Martha
# Hi, Martha
```
`input()` can also set a default message without using `print()`:
```python
>>> my_name = input('What is your name? ')  # default message
>>> print('Hi, {}'.format(my_name))
# What is your name? Martha
# Hi, Martha
```
***
int()
```python
>>> # transform a string to an integer
>>> from_integer = int('29')
>>> from_integer
# 29
>>> type(from_integer)
# <class 'int'>

>>> # transform a float to integer
>>> from_float = int(-3.14)
>>> from_float
# -3
>>> type(from_float)
# <class 'int'>

>>> # return an empty string
>>> int()
# 0
```
***
isinstance()
```python
>>> isinstance(1, int)
# True
>>> isinstance(1, str)
# False
```
***
iter()
```python
>>> i = iter([1, 2, 3])
>>> i
# <list_iterator object at 0x7f93158badc0>
>>> i.__next__()
# 1
>>> i.__next__()
# 2
>>> i.__next__()
# 3
```
***
len()
```python
>>> len('hello')
# 5

>>> len(['cat', 3, 'dog'])
# 3
```
***
list()
```python
>>> l = list()
>>> l
# []
>>> l.append(1)
>>> l.append(2)
>>> l
# [1, 2]
```
***
max()
```python
>>> max([1, 2, 10, 40, 5])
# 40
>>> max((1, 2, 10, 40, 5))
# 40
```
***
min()
```python
>>> min([1, 2, 10, 40, 5])
# 1
>>> min((1, 2, 10, 40, 5))
# 1
```
***
next()
```python
>>> i = iter([1, 2, 3])
>>> i
# <list_iterator object at 0x7f93158badc0>
>>> i.__next__()
# 1
>>> i.__next__()
# 2
>>> i.__next__()
# 3
```
***
oct()
```python
>>> oct(1)
# '0o1'
>>> oct(10)
# '0o12'
>>> oct(100)
# '0o144'
>>> oct(1000)
# '0o1750'
```
***
open()
```python
f = open("some_file.txt", "r")
```
***
ord()
```python
>>> ord('1')
# 49
>>> ord('a')
# 97
```
***
print()
The `print()` function writes the value of the argument(s) it is given. […] it handles multiple arguments, floating point-quantities, and strings. Strings are printed without quotes, and a space is inserted between items, so you can format things nicely:
```python
>>> print('Hello world!')
# Hello world!

>>> a = 1
>>> print('Hello world!', a)
# Hello world! 1
```
The keyword argument `end` can be used to avoid the newline after the output, or end the output with a different string:
```python
phrase = ['printed', 'with', 'a', 'dash', 'in', 'between']
>>> for word in phrase:
...     print(word, end='-')
...
# printed-with-a-dash-in-between-
```
The keyword `sep` specify how to separate the objects, if there is more than one:
```python
print('cats', 'dogs', 'mice', sep=',')
# cats,dogs,mice
```
***
property()
```python
>>> property(1)
# <property object at 0x7f93158c8590>
>>> property('1')
# <property object at 0x7f93159fe2c0>
```
***
reversed()
```python
>>> i = reversed([1, 2, 3])
>>> i.__next__()
# 3
>>> i.__next__()
# 2
>>> i.__next__()
# 1
>>> i
# <list_reverseiterator object at 0x7f93159ded00>
```
***
round()
```python
>>> round(1.4)
# 1
>>> round(1.5)
# 2
>>> round(2.1)
# 2
>>> round(2.9)
# 3
```
***
set()
```python
>>> s = set()
>>> s
# set()
>>> type(s)
# <class 'set'>
```
***
slice()
```python
>>> furniture = ['table', 'chair', 'rack', 'shelf']

>>> furniture[0:4]
# ['table', 'chair', 'rack', 'shelf']

>>> furniture[1:3]
# ['chair', 'rack']

>>> furniture[0:-1]
# ['table', 'chair', 'rack']

>>> furniture[:2]
# ['table', 'chair']

>>> furniture[1:]
# ['chair', 'rack', 'shelf']

>>> furniture[:]
# ['table', 'chair', 'rack', 'shelf']
```
Slicing the complete list will perform a copy:
```python
>>> spam2 = spam[:]
# ['cat', 'bat', 'rat', 'elephant']

>>> spam.append('dog')
>>> spam
# ['cat', 'bat', 'rat', 'elephant', 'dog']

>>> spam2
# ['cat', 'bat', 'rat', 'elephant']
```
***
sorted()
```python
>>> sorted([1, 2, 3, 7, 4])
# [1, 2, 3, 4, 7]

>>> sorted(['a', 'h', 'e'])
# ['a', 'e', 'h']

>>> sorted([1, 2, 3, 7, 4], reverse=True)
# [7, 4, 3, 2, 1]

>>> sorted(['a', 'h', 'e'], reverse=True)
# ['h', 'e', 'a']
```
***
str()
```python
>>> # transform an integer to a string
>>> from_integer = str(29)
>>> from_integer
# '29'
>>> type(from_integer)
# <class 'str'>

>>> # transform a float to string
>>> from_float = str(-3.14)
>>> from_float
# '-3.14'
>>> type(from_float)
# <class 'str'>

>>> # return an empty string
>>> str()
# ''
```
***
sum()
```python
>>> sum([2, 4, 6])
# 12
>>> sum([10, 10, 10])
# 30
```
***
tuple()
```python
>>> t = tuple()
>>> type(t)
# <class 'tuple'>
>>> t
# ()

>>> l = [1, 2, 3]
>>> tuple(l)
# (1, 2, 3)
```
***
type()
```python
>>> type('span')
# <class 'str'>

>>> type(99)
# <class 'int'>

>>> type(1.1)
# <class 'float'>

>>> type([1, 2])
# <class 'list'>

>>> type((1, 2))
# <class 'tuple'>

>>> type({1, 2})
# <class 'set'>

>>> type({'a': 1, 'b': 2})
# <class 'dict'>
```
***
zip()
```python
>>> furniture = ['table', 'chair', 'rack', 'shelf']
>>> price = [100, 50, 80, 40]
>>>
>>> for item, amount in zip(furniture, price):
...     print(f'The {item} costs ${amount}')
# The table costs $100
# The chair costs $50
# The rack costs $80
# The shelf costs $40
```
