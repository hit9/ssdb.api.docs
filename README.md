SSDB API Docs
=============

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

Status maybe `"ok"`, `"not_found"` or `"client_error"` etc.

Table Of Contents:

- [Prewords](#prewords)
- [set](#set)
- [setx](#setx)
- [expire](#expire)
- [ttl](#ttl)
- [setnx](#setnx)
- [get](#get)
- [getset](#getset)
- [del](#del)
- [incr](#incr)
- [exists](#exists)


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

ttl
----

Get the time to live for a key.

```
ttl key
```

Return seconds for the key to live, `-1` if the key dosent exist or has no associated expire.

```python
>>> ssdb.ttl('key')
99
>>> ssdb.ttl('key-not-exist')
-1
```

setnx
-----

Set the string value of a key only if the key dosent exist.

```
setnx key value
```

Return `1` for success, `0` if the key already exists.

```python
>>> ssdb.setnx('key-not-exist', 1)
1
>>> ssdb.setnx('key', 1)  # key already exists
0
```

get
---

Get the string value of a key.

```
get key
```

Return the string value of the key, `null` if the key dosent exist.

```python
>>> ssdb.get('key')
'body'
>>> ssdb.get('key-not-exsit') is None
True
```

getset
------

Set the string value of a key and return its old value.

```
getset key value
```

Return the key's old value, `null` if the key dosent exist.

```python
>>> ssdb.getset('key', 'new-value')
'old-value'
>>> ssdb.getset('key-not-exist', 'new-value') is None
True
```

del
---

Delete a key.

```
del key
```

Return `1` no matter whether the key exists or not.

```python
>>> ssdb.delete('key')
1
>>> ssdb.delete('key-not-exist')
1  # you cannt test if a key exists by this value
```

incr
----

Increment the integer value of a key by the given number.

```
incr key number
```

Return the value after the increment operation.

- If key dosent exist, set this key to `0` and increment it by `number`.
- If the value isnt a numberic string, cast it to integer and do increment
operation.

```python
>>> ssdb.set('key', '1val')
1
>>> ssdb.incr('key', 2)
3
>>> ssdb.incr('key', 1)
4
```
