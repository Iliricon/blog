---
title: "Python snippets"
date: 2018-01-16
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
