---
title: "Python snippets"
date: 2018-02-19
tags: ["python"]
description: Interesting programming snippets from the language we all love.
draft: false
---

This is a post where I aggregate useful python snippets that I use regularly and forget again anyways. Have fun, maybe there is something useful for you too!

### Reimporting a module in Python
```python
import importlib
importlib.reload(some_module)
```

This will reload a module with all changes made to the file. If you reload a module that you gave an alias to, you have to reimport the alias to make it work.

```python
import my_module as m
import importlib

importlib.reload(m)
```

### Going straight to a command line from a script
Basically this fancy line of code opens a python command line from your script and copies all current locals (e.g. variables, imported libraries, ect.). It is super useful for debugging and for automating the boring parts when you still need to experiment around.

Credit goes to @miterion <3

```python
from code import interact

# .... somewhere in your code
interact(local=locals())
```

### Finding the insertion point in an array
When you want to find the first element in a list that is smaller then an item, you can use the python bisect module. This handy module can find the proper insertion points for items in lists and also do the insertion in one function. It is super useful for inserting into sorted lists, or looking for thresholds.

```python
import bisect

l = [1,2,3]
bisect.bisect_left(l,0) # output 0
bisect.bisect_left(l, 2.5) # output 2
```
