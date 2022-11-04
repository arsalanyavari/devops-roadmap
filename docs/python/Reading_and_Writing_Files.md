# Reading and Writing Files
## The file Reading/Writing process
To read/write to a file in Python, you will want to use the `with` statement, which will close the file for you after you are done, managing the available resources for you.

## Opening and reading files
The `open` function opens a file and return a corresponding file object.
```python
>>> with open('C:\\Users\\your_home_folder\\hi.txt') as hello_file:
...     hello_content = hello_file.read()
...
>>> hello_content
'Hello World!'
```
Alternatively, you can use the readlines() method to get a list of string values from the file, one string for each line of text:
```python
>>> with open('sonnet29.txt') as sonnet_file:
...     sonnet_file.readlines()
...
# [When, in disgrace with fortune and men's eyes,\n',
# ' I all alone beweep my  outcast state,\n',
# And trouble deaf heaven with my bootless cries,\n', And
# look upon myself and curse my fate,']
```
You can also iterate through the file line by line:
```python
>>> with open('sonnet29.txt') as sonnet_file:
...     for line in sonnet_file:
...         print(line, end='')
...
# When, in disgrace with fortune and men's eyes,
# I all alone beweep my outcast state,
# And trouble deaf heaven with my bootless cries,
# And look upon myself and curse my fate,
```
## Writing to files
```python
>>> with open('bacon.txt', 'w') as bacon_file:
...     bacon_file.write('Hello world!\n')
...
# 13

>>> with open('bacon.txt', 'a') as bacon_file:
...     bacon_file.write('Bacon is not a vegetable.')
...
# 25

>>> with open('bacon.txt') as bacon_file:
...     content = bacon_file.read()
...
>>> print(content)
# Hello world!
# Bacon is not a vegetable.
```
