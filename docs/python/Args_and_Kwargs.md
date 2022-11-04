# Args and Kwargs
Python args and kwargs Made Easy
`*args` and `**kwargs` may seem scary, but the truth is that they are not that difficult to grasp and have the power to grant your functions with lots of flexibility.
Read the article [Python \*args and \*\*kwargs Made Easy](Python_*args_and_**kwargs_Made_Easy.md) for a more in deep introduction.

## Args and Kwargs
`*args` and `**kwargs` allow you to pass an undefined number of arguments and keywords when calling a function.
```python
>>> def some_function(*args, **kwargs):
...     pass
...
>>> # call some_function with any number of arguments
>>> some_function(arg1, arg2, arg3)

>>> # call some_function with any number of keywords
>>> some_function(key1=arg1, key2=arg2, key3=arg3)

>>> # call both, arguments and keywords
>>> some_function(arg, key1=arg1)

>>> # or none
>>> some_function()
```
**Python conventions**
The words `*args` and `**kwargs` are conventions. They are not imposed by the interpreter, but considered good practice by the Python community.
## args
You can access the arguments through the `args` variable:
```python
>>> def some_function(*args):
...     print(f'Arguments passed: {args} as {type(args)}')
...
>>> some_function('arg1', 'arg2', 'arg3')
# Arguments passed: ('arg1', 'arg2', 'arg3') as <class 'tuple'>
```
## kwargs
Keywords are accessed through the `kwargs` variable:
```python
>>> def some_function(**kwargs):
...     print(f'keywords: {kwargs} as {type(kwargs)}')
...
>>> some_function(key1='arg1', key2='arg2')
# keywords: {'key1': 'arg1', 'key2': 'arg2'} as <class 'dict'>
```
