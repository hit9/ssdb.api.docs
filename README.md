SSDB API Docs
=============

Table Of Contents:

1. [Prewords](#prewords)
2. [set](#set)

Prewords
--------

All examples are in Python, via
[ssdb.py](https://github.com/hit9/ssdb.py).

SSDB responses follows this pattern:

```
status [value]
```

Status maybe `"ok", "not_found"` or `"client_error"` etc.

set
---

Set the string value of a key.

Proto:
```
set key value
```

Return value:
```
status 
```

Example:

```python
>>> ssdb.set('key', 'val')
1
```
