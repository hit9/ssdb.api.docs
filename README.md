SSDB API Docs
=============

Table Of Contents:

1. [Prewords](#prewords)
2. [set](#set)
3. [setx](#setx)

Prewords
--------

This is an unofficial documentation for [ssdb](https://github.com/ideawu/ssdb),
which is a fast nosql database with a variety of data structures,
welcome to fork and send me pull requests.

All examples are in Python, via
[ssdb.py](https://github.com/hit9/ssdb.py).

Ssdb responses follows this pattern:

```
status [value]
```

Status maybe `"ok", "not_found"` or `"client_error"` etc.

set
---

Set the string value of a key.

```
set key value
```

Return `1` for success.

```python
>>> ssdb.set('key', 'val')
1
```

setx
----

Set the string value and expire time of a key.

```
setx key value seconds
```

Return `1` for success.

expire
------

Set a key's time to live in seconds.

```
expire key seconds
```

If key exists and `expire` called success, return `1`, else return `0`.

```python
>>> ssdb.expire('key', 5)
1
>>> ssdb.expire('key-not-exist', 5)
0
```
