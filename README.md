# CJK Format

This Python 3 module provides utility functions for formatting fixed-width CJK strings.

## Installation

This module is published in [PyPI](https://pypi.org/project/cjkformat/), so you can install it using `pip3`:
```bash
pip3 install --user cjkformat
```

Yeah, there is no version 0.1.0 there. I had to make a small fix, but I didn't know that the same version is never allowed again even after the project is deleted. I see one typo there in README.md, but who cares!

## Introduction

One of the most important features of this module is to align Latin and CJK characters using `%{width}s` specifiers. For example, you may expect the following lines
```python
print('%-10s|%-10s|' % ('ab', 'cd'))
print('%-10s|%-10s|' % ('가나다라', '마바사아'))
```
to produce

![Aligned output](aligned.png "Aligned output")

because both lines use the same-width string specifiers (`%10s`). However, this code snippet will produce

![Misaligned output](misaligned.png "Misaligned output")

even though the pipe characters are vertically (horizontally?) aligned between the two `print` lines. This misalignment occurs because wide CJK characters are considered one character (e.g, `len('가')=1`) even though they take up two column spaces. To resolve this alignment issue, the width component of the string specifier needs to be adjusted using the number of actual CJK characters in the argument string. The above example can be fixed by reducing the width 10 to 10 - 4 (four CJK characters in each `%10s`) as follows:
```python
print('%-10s|%-10s|' % ('ab', 'cd'))
print('%-6s|%-6s|' % ('가나다라', '마바사아'))
```
which will print nicely aligned

![Aligned output](aligned.png "Aligned output")

However, it would be very cumbersome to count CJK characters and adjust widths in the format string every time we use CJK characters. I found some solutions from these articles:
* [CJKStr -- A Simple Package for Processing CJK string](https://pypi.org/project/cjkstr/)
* [Python unicodedata.east_asian_width() Examples](https://www.programcreek.com/python/example/5938/unicodedata.east_asian_width) and
* [파이썬(Python)으로 한글 print format 설정 시 padding이 잘 맞지 않을 때](https://sarc.io/development/810-python-print-format-padding),

but I was not happy with any of those solutions because they deviated too much from the usage of `print()` and disrupt regular patterns of `print()`. It would also be hard to justify use of those functions to non-CJK developers when they have very different calling patterns.

In this module, I tried to mimic the usage of `print()` as much as possible. Since the `%` operator is intereted first before being passed to any functions, I was not able to use exactly the same syntax as `print()`. Instead, I tried to keep the name of the core function short and simple. Indeed, its name is simply `f`, which is the same as the prefix for the f-string syntax. I also defined `printf()` that behaves much like the `printf()` function in the C language. This function combines `print(, end='')` and `f()` to not add a newline.

Now, using the new `f()` function, the above example would be
```python
from cjkformat import f

print(f('%-10s|%-10s|', 'ab', 'cd'))
print(f('%-10s|%-10s|', '가나다라', '마바사아'))
```
which will print

![Aligned output](aligned.png "Aligned output")

Note that the `f()` function takes both the format and arguments. Also, unlike `print()`, it takes a variable number of arguments instead of a list of arguments.

Equivalently, using `printf()`,
```python
from cjkformat import printf

printf('%-10s|%-10s|\n', 'ab', 'cd')
printf('%-10s|%-10s|\n', '가나다라', '마바사아')
```
will produce the same output:

![Aligned output](aligned.png "Aligned output")
