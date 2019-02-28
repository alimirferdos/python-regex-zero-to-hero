# A Quick zero-to-here Guide to regex in Python
This is a quick introduction to regex in python. It's not a simple regex reference nor a complicated one.  
I have gathered and summarized what I've learned from different references about this topic so I think it can be used as a place to get what you need for most use cases.
It has also been used as a lecture note for 2 lectures I gave at K. N. Toosi University of Technology.
# 1. Introduction to regex & Python Methods
In this section we'll see how regex can be used in python.
### Why to Use Regex: 
First of all, regex generally is used for two reasons:
 1. Verifying a pattern
 2. Find & Replace

### Using it in Python
Python Supports regex through `re` module which is a part of The Python Standard Library. Just import it:
```python
import re
```

It is a best practice to use a raw string because it doesn't escape anything (i.e. backslashes and special metacharacters) allowing you to pass them through directly to the regular expression engine.
So you can use `r"\n\w"` instead of `"\\n\\w"` as in other languages, which is much easier to read.

### Python Methods
As stated previously, regex is used for two main reasons:
####  1. Verifying a pattern
`re.match()` checks for "a" match only at the beginning of the string,
while `re.search()` checks for "a" match anywhere in the string:

```python
pattern = r"some"
if re.match(pattern, "othersomesomesome"):
    print("Match")
else:
    print("No match")
# No Match
```
```python
if re.search(pattern, "textsomeothersome"):
    print("Match")
else:
    print("No match")
# Match
```

The function `re.findall` returns a list of all substrings that match a pattern:
```python 
print(re.findall(pattern, "textsomeothersome"))
# ['some', 'some']
```

You can get the start and end indices with `start()` an `end()` methods:
```python 
match = re.search(pattern, "textsomeother")
if match:
    print(match.start())
    print(match.end())
    print(match.span())
# 4
# 8
# (4, 8)
```

#### 2. Find & Replace
You can use the `sub()` method to find a pattern in a string and replace it with a new string. You can also define the max. count of replacement:
```python 
s = "My name is Ali. Hi Ali."
pattern = r"Ali"
newstr = re.sub(pattern, "Mehdi", s, count=5)
print(newstr)
# My name is Mehdi. Hi Mehdi.
```
# 2. Basic Metacharacters
Metacharacters are what make regular expressions more powerful than normal string methods. This section deals with the basic metacharacters you can use.
#### . (dot) 
Matches any character, except a new line:
```python 
pattern = r"gr.y"
if re.match(pattern, "grey"):
    print("Match 1")
if re.match(pattern, "gray"):
    print("Match 2")
if re.match(pattern, "blue"):
    print("Match 3")
# Match 1
# Match 2
```
#### ^ and $: 
These match the start and end of a string, respectively. You can define a pattern like this:
```python 
pattern = r"^gr.y$"
```
The following example checks if the string ends in "ly":
```python 
pattern = r".*ly$" # don't mind the * operator for now!
if re.match(pattern, "beautifully"):
    print("Match 1")
if re.match(pattern, "not a ly at the end!"):
    print("Match 2")

# Match 1
```
#### |  (Pipe)
It means or:
```python 
pattern = r"gr(a|e)y"
match = re.match(pattern, "gray")
if match:
    print("Match 1")
match = re.match(pattern, "grey")
if match:
    print("Match 2")
match = re.match(pattern, "griy")
if match:
    print("Match 3")

# Match 1
# Match 2
```
### Repetitions
#### * (star)
It means zero or more repetitions of the previous thing.
The "previous thing" in * can be a single character, a class, or a group of characters in parentheses.
Let's examine the example we saw earlier:
```python 
pattern = r".*ly$"
```
It is saying that consider any character (dot). It can have zero or more repetitions followed by "ly" at the end. In every repetition the character is derived from "dot". Note that this doesn't means that the characters have to be same!
### + operator
It means "one" or more repetitions of the previous thing.
```python
pattern = r"egg(spam)*"
if re.match(pattern, "egg"):
    print("Match 1")
if re.match(pattern, "eggspamspamegg"):
    print("Match 2")
if re.match(pattern, "spam"):
    print("Match 3")
# Match 2
```
### ? (Question Mark)
It means "zero or one repetitions" of the previous thing. In other words it means that this part is "optional"!
```python 
pattern = r"ice(-)?cream"
if re.match(pattern, "ice-cream"):
    print("Match 1")
if re.match(pattern, "icecream"):
    print("Match 2")
if re.match(pattern, "sausages"):
    print("Match 3")
if re.match(pattern, "ice--ice"):
    print("Match 4")
# Match 1
# Match 2
```
### {x,y}
The regex {x,y} means "between x and y repetitions of something" where x and y are both included. 
If the first number is missing, it is taken to be zero. If the second number is missing, it is taken to be infinity.
```python 
pattern = r"9{1,3}$"
if re.match(pattern, "9"):
    print("Match 1")
if re.match(pattern, "999"):
    print("Match 2")
if re.match(pattern, "9999"):
    print("Match 3")
# Match 1
# Match 2
```
# 3. Character classes
A character class is created by putting the characters it matches inside square brackets. They mean any character from the given class can be used.
The hyphen (-) can be used to indicate ranges in character classes:
```python 
pattern = r"[A-Z][A-Z][0-9]"
if re.search(pattern, "LS8"):
    print("Match 1")
if re.search(pattern, "E3"):
    print("Match 2")
if re.search(pattern, "1ab"):
    print("Match 3")
# Match 1
```
Multiple ranges can be included in one class. For example, [A-Za-z] matches a letter of any case.
The pattern [^A-Z] excludes uppercase strings. But the ^ should be inside the brackets to invert the character class.
# 4. Groups
The content of groups in a match can be accessed using the group function. A Group is made by using parentheses. 
Note that Groups can be nested as well! In this case, the child group's number comes after the parent's one.
- A call of group(0) or group() returns the whole match. 
- A call of group(n), where n is greater than 0, returns the nth group from the left. 
- The method groups() returns all groups up from 1 in the form of a tuple.
 
Groups can be used to access different parts of the patterns:
```python 
pattern = r"a(bc)(de)(f(g)h)i"
match = re.match(pattern, "abcdefghijklmnop")
if match:
    print("General Group() Call" + match.group())
    print("Group 0: " + match.group(0))
    print("Group 1: " + match.group(1))
    print("Group 3: " + match.group(2))
    print("All Groups: " + match.groups())
    
# General Group() Callabcdefghi
# Group 0: abcdefghi
# Group 1: bc
# Group 3: de
# All Groups: ('bc', 'de', 'fgh', 'g')
```
### Named Groups
Named groups have the format `(?P<name>...)`, where name is the name of the group, and ... is the content.
They behave exactly the same as normal groups, except they can be accessed by `group(name)` in addition to its number.
```python 
pattern = r"^(?P<Area_Code>09(12|35))"
re.match(pattern, "09358888888").group("Area_Code")
re.match(pattern, "09128888888").group("Area_Code")
# 0935
# 0912
```
### Non-capturing groups
Non-capturing groups have the format `(?:...)`. They are not accessible by the group method, so they can be added to an existing regular expression without breaking the numbering.
```python 
pattern = r"(?:https?|ftp)://(?P<host>[^/\r\n]+)(/[^\r\n]*)?"
match = re.match(pattern, "https://courses.kntu.ac.ir/login/index.php")
if match:
    print(match.group("host"))
    print(match.groups())
    
# courses.kntu.ac.ir
# ('courses.kntu.ac.ir', '/login/index.php')
```

# 5. Special Sequences

Some of most useful special sequences are `\d`, `\s`, and `\w`.
These match digits, whitespace, and word characters respectively. 
Versions of these special sequences with upper case letters - `\D`, `\S`, and `\W` -mean the opposite to the lower-case versions. For instance, \D matches anything that isn't a digit.
```python 
pattern = r"(\D+\d)" 
# one or more repetitions of a non digit followed by a digit

match = re.match(pattern, "Hi 999!")
if match:
    print("Match 1")
match = re.match(pattern, "1, 23, 456!")
if match:
    print("Match 2")
match = re.match(pattern, " ! $?")
if match:
    print("Match 3")
match = re.match(pattern, "hi!5")
if match:
    print("Match 4")
# Match 1
# Match 4
```
Another special sequence is a backslash and a number between 1 and 99, e.g., `\1` or `\17`.
This matches the expression of the group of that number.
Note, that "(.+) \1" is not the same as "(.+) (.+)", because \1 refers to the first group's subexpression, which is the matched expression itself, and not the regex pattern.
```python 
pattern = r"(.+) \1"
match = re.match(pattern, "word word")
if match:
    print("Match 1")
match = re.match(pattern, "?! ?!")
if match:
    print("Match 2")
match = re.match(pattern, "abc cde")
if match:
    print("Match 3")

# Match 1
# Match 2
```
# 5. Look-around Assertion
Look-ahead and look-behind assertions are available in both positive and negative form. They are used for advanced searches.
They do not consume characters in the string, but only "assert" whether a match is possible or not.

### Positive look-ahead assertion `(?=...)`
It is used when you want to match something followed by something else.
Once the contained expression has been tried, the matching engine doesn’t advance at all; the rest of the pattern is tried right where the assertion started.
For example `d(?=r)` matches a d only if is followed by r, but r will not be part of the overall regex match.

In the following example we are looking for a number followed by "dollars":
```python 
pattern = r"\d+(?= dollars)"
match = re.match(pattern, "10 dollars")
if match:
    print("Match 1")
match = re.match(pattern, "10 rials")
if match:
    print("Match 2")
match = re.match(pattern, "10 nice dollars")
if match:
    print("Match 3")

# Match 1
```
### Negative look-ahead assertion `(?!...)`
This is the opposite of the positive assertion; it is used when you want to match something *not* followed by something else.
For example `d(?!r)` matches a d only if is *not* followed by r, but r will not be part of the overall regex match (r is only asserted!)

Let's say we want to accept all file names except `.bat` files.
`.*[.](?!bat$)[^.]*$`  The negative look-ahead means: if the expression  `bat`  doesn’t match at this point, try the rest of the pattern; if  `bat$`  does match, the whole pattern will fail. The trailing  `$`  is required to ensure that something like  `sample.batch`, where the extension only starts with  `bat`, will be allowed. The  `[^.]*`  makes sure that the pattern works when there are multiple dots in the filename.

Excluding another filename extension is now easy; simply add it as an alternative inside the assertion. The following pattern excludes filenames that end in either  `bat`  or  `exe`:
`.*[.](?!bat$|exe$)[^.]*$`

### Positive look-behind assertion `(?<=...)`
`(?<=r)d` matches a d only if is preceded by an r, but r will not be part of the overall regex match

### Negative look-behind assertion `(?<!...)`
`(?<!r)d`matches a d only if is not preceded by an r, but r will not be part of the overall regex match

# 6. Real-World Examples
In this section we'll check out some practical examples. The examples start from easy and go harder as we proceed.
## 1. Reordering the Date
Given a string of dates where "day" comes after "month", we want to reorder it so "day" comes before "month"!
```python 
regex = r"([a-zA-Z]+) (\d+)"
print(re.sub(regex, r"\2 of \1", "June 24, August 9, Dec 12"))

# 24 of June, 9 of August, 12 of Dec
```
   
## 2. Simple Email Extraction
```python 
pattern = r"([\w]+[\w\.-]+)@([\w\.-]+)(\.[\w\.]+)"
s = "Please contact alimirferdos@email.kntu.ac.ir for assistance"
match = re.search(pattern, s)
if match:
    print(match.group())
```
The regex above says that the string should contain a word (with dots and dashes allowed), followed by the @ sign, then another similar word, then a dot and another word.
## 3. Password Strength
Let's say we only accept passwords on our website where they follow these rules:
- At least 8 characters
- Must have at least one uppercase letter
- Must have at least one lower case letter
- Must have at least one digit
- Should contain other characters
```python 
pattern = r"^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$"
s = "Y5Ak=Zj_"
match = re.search(pattern, s)
if match:
    print(match.group())
```
Here we are using multiple positive look-ahead where the order of the conditions doesn’t affect the result.

## 4. Advanced URL Matching
- Must start with http or https or ftp followed by ://
- Must match a valid domain name
- Could contain a port specification (http://www.example.com:80)
- Could contain digit, letter, dots, hyphens, forward slashes, multiple times
```python 
pattern = r"^(http|https|ftp):[\/]{2}" \
          r"([a-zA-Z0-9][a-zA-Z0-9\-\.]+\.[a-zA-Z]{2,})" \
          r"(:[0-9]+)?" \
          r"\/?" \
          r"([a-zA-Z0-9\-\._\?\,\'\/\\\+&amp;%\$#\=~]*)"  # a-z, A-Z, 0-9, the characters: -._?,'/\+&amp;%$#=~.

match = re.match(pattern, "https://courses.kntu.ac.ir:2000/login/index.php?id=5&user=ali")
if match:
    print(match.groups())

# ('https', 'courses.kntu.ac.ir', ':2000', 'login/index.php?id=5&user=ali')
```
## 5. Simple HTML Tag Matching
Parsing HTML using regex is very hard because there are many different situations where it's not standard but is valid! Here we are considering a very simple case. 
We are looking for tags where there are no extra spaces and are not single tags.
```python 
pattern = r"<([\w]+).*>([\w\s]*?)<\/\1>"
match = re.match(pattern, "<div class=\"placeholder\">some text\nsome other text</div>")
if match:
    print(match.group())
```
## 6. Finding Duplicate Words
We want to match every duplication of the words (non-consecutive ones as well) assuming the words are space separated.
```python 
pattern = r"\b(\w+)\b(?=.*\1)"
match = re.match(pattern, "Regular expressions are double-edged swords. The more complexity is added, "
                          "the more difficult it is to solve the problem.")
if match:
    print(match.group())
```
Here we are using word boundaries. `\b` mainly checks positions. It matches when a word character (i.e.: abcDE) is followed by a non-word character (Ie: -~,!).
For example Given the phrase "Regular expressions are awesome", the pattern `"\bare\b"` matches "are". 
So we're matching every word character followed by a non-word character (in our case space) and then check if the matched word is already present or not.

# 7. Where to Go from here
This was a starter guide to regex and can help you out in most cases but there are more advanced concepts to learn. 
[Awesome-regex](https://github.com/aloisdg/awesome-regex) has a great list of Regex libraries, tools, frameworks, books and more! So definitely check it out!
I also used [this](https://www.sitepoint.com/demystifying-regex-with-practical-examples/)  for some of the examples.

## Contribution
-   Star the repo
-   Open pull request 
-   Tell someone who needs this
- Give feedback at [![](https://img.shields.io/twitter/follow/alimirferdos.svg?label=alimirferdos&style=plastic)](https://github.com/badges/shields/graphs/contributors)
