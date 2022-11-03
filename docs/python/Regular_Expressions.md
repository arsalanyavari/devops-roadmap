#Regular Expressions

A regular expression (shortened as regex [...]) is a sequence of characters that specifies a search pattern in text. [...] used by string-searching algorithms for "find" or "find and replace" operations on strings, or for input validation.

1. Import the regex module with `import re`.

2. Create a Regex object with the `re.compile()` function. (Remember to use a raw string.)

3. Pass the string you want to search into the Regex object’s `search()` method. This returns a `Match` object.

4. Call the Match object’s `group()` method to return a string of the actual matched text.

All the regex functions in Python are in the re module:
```python
>>> import re
```
## Regex symbols
| Symbol              |  Matches                                                |
|         :---:       |                     :---:                               |
| ?                   |  zero or one of the preceding group.                    |
| *                   |  zero or more of the preceding group.                   |
| +                   |  one or more of the preceding group.                    |
| {n}                 |  exactly n of the preceding group.                      |
| {n,}                |  n or more of the preceding group.                      |
| {,m}                |  0 to m of the preceding group.                         |
| {n,m}               |  at least n and at most m of the preceding p.           |
| {n,m}? or *? or +?  |  performs a non-greedy match of the preceding p.        |
| ^spam               |  means the string must begin with spam.                 |
| spam$               |  means the string must end with spam.                   |
| .                   |  any character, except newline characters.              |
| \d, \w, and \s      |  a digit, word, or space character, respectively.       |
| \D, \W, and \S      |  anything except a digit, word, or space, respectively. |
| [abc]               |  any character between the brackets (such as a, b, ).   |
| [^abc]              |  any character that isn’t between the brackets.         |

## Matching regex objects
```python
>>> phone_num_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d')

>>> mo = phone_num_regex.search('My number is 415-555-4242.')

>>> print(f'Phone number found: {mo.group()}')
# Phone number found: 415-555-4242
```
## Grouping with parentheses
```python
>>> phone_num_regex = re.compile(r'(\d\d\d)-(\d\d\d-\d\d\d\d)')
>>> mo = phone_num_regex.search('My number is 415-555-4242.')

>>> mo.group(1)
# '415'

>>> mo.group(2)
# '555-4242'

>>> mo.group(0)
# '415-555-4242'

>>> mo.group()
# '415-555-4242'
```
To retrieve all the groups at once use the `groups()` method:
```python
>>> mo.groups()
('415', '555-4242')

>>> area_code, main_number = mo.groups()

>>> print(area_code)
415

>>> print(main_number)
555-4242
```
## Multiple groups with Pipe
You can use the `|` character anywhere you want to match one of many expressions.
```python
>>> hero_regex = re.compile (r'Batman|Tina Fey')

>>> mo1 = hero_regex.search('Batman and Tina Fey.')
>>> mo1.group()
# 'Batman'

>>> mo2 = hero_regex.search('Tina Fey and Batman.')
>>> mo2.group()
# 'Tina Fey'
```
You can also use the pipe to match one of several patterns as part of your regex:
```python
>>> bat_regex = re.compile(r'Bat(man|mobile|copter|bat)')
>>> mo = bat_regex.search('Batmobile lost a wheel')

>>> mo.group()
# 'Batmobile'

>>> mo.group(1)
# 'mobile'
```
## Optional matching with the Question Mark
The `?` character flags the group that precedes it as an optional part of the pattern.
```python
>>> bat_regex = re.compile(r'Bat(wo)?man')

>>> mo1 = bat_regex.search('The Adventures of Batman')
>>> mo1.group()
# 'Batman'

>>> mo2 = bat_regex.search('The Adventures of Batwoman')
>>> mo2.group()
# 'Batwoman'
```
## Matching zero or more with the Star
The `*` (star or asterisk) means “match zero or more”. The group that precedes the star can occur any number of times in the text.
```python
>>> bat_regex = re.compile(r'Bat(wo)*man')
>>> mo1 = bat_regex.search('The Adventures of Batman')
>>> mo1.group()
'Batman'

>>> mo2 = bat_regex.search('The Adventures of Batwoman')
>>> mo2.group()
'Batwoman'

>>> mo3 = bat_regex.search('The Adventures of Batwowowowoman')
>>> mo3.group()
'Batwowowowoman'
```
## Matching one or more with the Plus
The `+` (or plus) means match one or more. The group preceding a plus must appear at least once:
```python
>>> bat_regex = re.compile(r'Bat(wo)+man')

>>> mo1 = bat_regex.search('The Adventures of Batwoman')
>>> mo1.group()
# 'Batwoman'

>>> mo2 = bat_regex.search('The Adventures of Batwowowowoman')
>>> mo2.group()
# 'Batwowowowoman'

>>> mo3 = bat_regex.search('The Adventures of Batman')
>>> mo3 is None
# True
```
## Matching specific repetitions with Curly Brackets
If you have a group that you want to repeat a specific number of times, follow the group in your regex with a number in curly brackets:
```python
>>> ha_regex = re.compile(r'(Ha){3}')

>>> mo1 = ha_regex.search('HaHaHa')
>>> mo1.group()
# 'HaHaHa'

>>> mo2 = ha_regex.search('Ha')
>>> mo2 is None
# True
```
Instead of one number, you can specify a range with minimum and a maximum in between the curly brackets. For example, the regex (Ha){3,5} will match ‘HaHaHa’, ‘HaHaHaHa’, and ‘HaHaHaHaHa’.
```python##
>>> ha_regex = re.compile(r'(Ha){2,3}')
>>> mo1 = ha_regex.search('HaHaHaHa')
>>> mo1.group()
# 'HaHaHa'
```
## Greedy and non-greedy matching
Python’s regular expressions are greedy by default: in ambiguous situations they will match the longest string possible. The non-greedy version of the curly brackets, which matches the shortest string possible, has the closing curly bracket followed by a question mark.
```python
>>> greedy_ha_regex = re.compile(r'(Ha){3,5}')

>>> mo1 = greedy_ha_regex.search('HaHaHaHaHa')
>>> mo1.group()
# 'HaHaHaHaHa'

>>> non_greedy_ha_regex = re.compile(r'(Ha){3,5}?')
>>> mo2 = non_greedy_ha_regex.search('HaHaHaHaHa')
>>> mo2.group()
# 'HaHaHa'
```
## The findall() method
The `findall()` method will return the strings of every match in the searched string.
```python
>>> phone_num_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') # has no groups

>>> phone_num_regex.findall('Cell: 415-555-9999 Work: 212-555-0000')
# ['415-555-9999', '212-555-0000']
```
## Making your own character classes
You can define your own character class using square brackets. For example, the character class [aeiouAEIOU] will match any vowel, both lowercase and uppercase.
```python
>>> vowel_regex = re.compile(r'[aeiouAEIOU]')
>>> vowel_regex.findall('Robocop eats baby food. BABY FOOD.')
# ['o', 'o', 'o', 'e', 'a', 'a', 'o', 'o', 'A', 'O', 'O']
```
You can also include ranges of letters or numbers by using a hyphen. For example, the character class [a-zA-Z0-9] will match all lowercase letters, uppercase letters, and numbers.

By placing a caret character (`^`) just after the character class’s opening bracket, you can make a negative character class that will match all the characters that are not in the character class:
```python
>>> consonant_regex = re.compile(r'[^aeiouAEIOU]')
>>> consonant_regex.findall('Robocop eats baby food. BABY FOOD.')
# ['R', 'b', 'c', 'p', ' ', 't', 's', ' ', 'b', 'b', 'y', ' ', 'f', 'd', '.', '
# ', 'B', 'B', 'Y', ' ', 'F', 'D', '.']
```
## The Caret and Dollar sign characters
You can also use the caret symbol `^` at the start of a regex to indicate that a match must occur at the beginning of the searched text.

Likewise, you can put a dollar sign `$` at the end of the regex to indicate the string must end with this regex pattern.

And you can use the `^` and `$` together to indicate that the entire string must match the regex.

The `r'^Hello’` regular expression string matches strings that begin with ‘Hello’:
```python
>>> begins_with_hello = re.compile(r'^Hello')
>>> begins_with_hello.search('Hello world!')
# <_sre.SRE_Match object; span=(0, 5), match='Hello'>

>>> begins_with_hello.search('He said hello.') is None
# True
```
The `r'\d\$'` regular expression string matches strings that end with a numeric character from 0 to 9:
```python
>>> whole_string_is_num = re.compile(r'^\d+$')

>>> whole_string_is_num.search('1234567890')
# <_sre.SRE_Match object; span=(0, 10), match='1234567890'>

>>> whole_string_is_num.search('12345xyz67890') is None
# True

>>> whole_string_is_num.search('12 34567890') is None
# True
```
## The Wildcard character
The `.` (or dot) character in a regular expression will match any character except for a newline:
```python
>>> at_regex = re.compile(r'.at')

>>> at_regex.findall('The cat in the hat sat on the flat mat.')
['cat', 'hat', 'sat', 'lat', 'mat']
```
## Matching everything with Dot-Star
```python
>>> name_regex = re.compile(r'First Name: (.*) Last Name: (.*)')

>>> mo = name_regex.search('First Name: Al Last Name: Sweigart')
>>> mo.group(1)
# 'Al'

>>> mo.group(2)
'Sweigart'
```
The `.*` uses greedy mode: It will always try to match as much text as possible. To match any and all text in a non-greedy fashion, use the dot, star, and question mark (`.*?`). The question mark tells Python to match in a non-greedy way:
```python
>>> non_greedy_regex = re.compile(r'<.*?>')
>>> mo = non_greedy_regex.search('<To serve man> for dinner.>')
>>> mo.group()
# '<To serve man>'

>>> greedy_regex = re.compile(r'<.*>')
>>> mo = greedy_regex.search('<To serve man> for dinner.>')
>>> mo.group()
# '<To serve man> for dinner.>'
```
## Matching newlines with the Dot character
The dot-star will match everything except a newline. By passing `re.DOTALL` as the second argument to `re.compile()`, you can make the dot character match all characters, including the newline character:
```python
>>> no_newline_regex = re.compile('.*')
>>> no_newline_regex.search('Serve the public trust.\nProtect the innocent.\nUphold the law.').group()
# 'Serve the public trust.'

>>> newline_regex = re.compile('.*', re.DOTALL)
>>> newline_regex.search('Serve the public trust.\nProtect the innocent.\nUphold the law.').group()
# 'Serve the public trust.\nProtect the innocent.\nUphold the law.'
Case-Insensitive matching
To make your regex case-insensitive, you can pass re.IGNORECASE or re.I as a second argument to re.compile():

>>> robocop = re.compile(r'robocop', re.I)

>>> robocop.search('Robocop is part man, part machine, all cop.').group()
# 'Robocop'

>>> robocop.search('ROBOCOP protects the innocent.').group()
# 'ROBOCOP'

>>> robocop.search('Al, why does your programming book talk about robocop so much?').group()
# 'robocop'
```
## Substituting strings with the sub() method
The `sub()` method for Regex objects is passed two arguments:

1. The first argument is a string to replace any matches.

2.The second is the string for the regular expression.

The `sub()` method returns a string with the substitutions applied:
```python
>>> names_regex = re.compile(r'Agent \w+')

>>> names_regex.sub('CENSORED', 'Agent Alice gave the secret documents to Agent Bob.')
# 'CENSORED gave the secret documents to CENSORED.'
```
## Managing complex Regexes
To tell the `re.compile()` function to ignore whitespace and comments inside the regular expression string, “verbose mode” can be enabled by passing the variable `re.VERBOSE` as the second argument to `re.compile()`.

Now instead of a hard-to-read regular expression like this:
```python
phone_regex = re.compile(r'((\d{3}|\(\d{3}\))?(\s|-|\.)?\d{3}(\s|-|\.)\d{4}(\s*(ext|x|ext.)\s*\d{2,5})?)')
```
you can spread the regular expression over multiple lines with comments like this:
```python
phone_regex = re.compile(r'''(
    (\d{3}|\(\d{3}\))?            # area code
    (\s|-|\.)?                    # separator
    \d{3}                         # first 3 digits
    (\s|-|\.)                     # separator
    \d{4}                         # last 4 digits
    (\s*(ext|x|ext.)\s*\d{2,5})?  # extension
    )''', re.VERBOSE)
```
