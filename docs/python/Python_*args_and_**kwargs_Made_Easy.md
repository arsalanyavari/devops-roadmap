## Basics
The first thing you need to know is that `*args` and `**kwargs` lets you pass an undefined number of `arguments` and `keywords` when calling a [function](Functions.md):
```python
def some_function(*args, **kwargs):
    pass

# call some_function with any number of arguments
some_function(arg1, arg2, arg3)

# call some_function with any number of keywords
some_function(key1=arg1, key2=arg2, key3=arg3)

# call both, arguments and keywords
some_function(arg, key1=arg1)

# or none
some_function()
```
Second, the words `args` and `kwargs` are conventions. This means they are not imposed by the interpreter, but considered good practice among the Python community:

# This function would work just fine
```python
def some_function(*arguments, **keywords):
    pass
```
<!-- $\mathbb{\color{teal}{this \ is \ a } \ \color{magenta}{paragraph \ in \ another \ font}}$ -->
$\color[rgb]{0,1,1} Note$ $\color[rgb]{0,1,1} about$ $\color[rgb]{0,1,1} conventions:$
Even if the above function works, don't do it. Conventions are there to help you write readable code for you and anyone that might be interested in your project. Other conventions include the 4 space indentation, comments, and imports. Reading the [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/) is highly recommended.
So, how does Python know that we want our function to accept multiple arguments and keywords? Yes, the answers are the `*` and `**` operators.

Now that we have covered the basics, let’s work with them 👊.

## args
We now know how to pass multiple arguments using `*args` as a parameter to our functions, but how do we work with them? It’s easy: all the arguments are within the `args` variable as a tuple:
```python
def some_function(*args):
    print(f'Arguments passed: {args} as {type(args)}')


some_function('arg1', 'arg2', 'arg3')
# Arguments passed: ('arg1', 'arg2', 'arg3') as <class 'tuple'>
```
We can iterate over them:
```python
def some_function(*args):
    for a in args:
        print(a)


some_function('arg1', 'arg2', 'arg3')
# arg1
# arg2
# arg3
```
Access the elements with an index:
```python
def some_function(*args):
    print(args[1])


some_function('arg1', 'arg2', 'arg3')  # arg2
Slice:

def some_function(*args):
    print(args[0:2])


some_function('arg1', 'arg2', 'arg3')
# ('arg1', 'arg2')
```
Whatever you do with a **tuple**, you can do it with `args`.

## kwargs
While arguments are in the args variable, keywords are within `kwargs`, but this time as a [dictionary](Dictionaries.md) where the key is the keyword:
```python
def some_function(**kwargs):
    print(f'keywords: {kwargs} as {type(kwargs)}')


some_function(key1='arg1', key2='arg2', key3='arg3')
# keywords: {'key1': 'arg1', 'key2': 'arg2', 'key3': 'arg3'} as <class 'dict'>
```
Again, we can do with `kwargs` the same we would do with any dictionary.

Iterate over:
```python
def some_function(**kwargs):
    for key, value in kwargs.items():
        print(f'{key}: {value}')


some_function(key1='arg1', key2='arg2', key3='arg3')
# key1: arg1
# key2: arg2
# key3: arg3
```
Use the `get()` method:
```python
def some_function(key, **kwargs):
    print(kwargs.get(key))


some_function('key3', key1='arg1', key2='arg2', key3='arg3')
# arg3
```

# Conclusion
`*args` and `**kwargs` may seem scary, but the truth is that they are not that difficult to grasp and have the power to grant your functions with lots of flexibility. If you know about tuples and dictionaries, you are ready to go.

Want to play with args and kwargs? [This](https://mybinder.org/v2/gh/wilfredinni/python-cheatsheet/master?filepath=jupyter_notebooks) is an online Jupyter Notebook for you to try.
