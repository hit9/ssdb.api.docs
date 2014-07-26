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
- [getbit](#getbit)
- [setbit](#setbit)
- [countbit](#countbit)
- [substr](#substr)
- [strlen](#strlen)
- [keys](#keys)
- [scan](#scan)
- [rscan](#rscan)
- [multi_set](#multi_set)
- [multi_get](#multi_get)
- [multi_del](#multi_del)

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

exists
------

Determine if a key exists.

```
exists key
```

Return `1` if key exists, else `0`.

```python
>>> ssdb.exists('key')
True
```

getbit
------

Returns the bit value at offset in the string value stored at key.

```
getbit key offset
```

Return the bit value(`1` or `0`).

- If key dosent exist, return `0`
- If `offset` exceeds the length of the value, return `0`

```python
>>> ssdb.set('key', 'val')
1
>>> ssdb.getbit('key', 2)
1
```

setbit
------

Sets the bit at offset in the string value

```
setbit key offset bit
```

Return the old bit value.


```
>>> ssdb.set('key', 'val')
1
>>> ssdb.setbit('key', 2, 0)
1
>>> ssdb.get('key')
'ral'
```

countbit
--------

Count `1` in the string value of a key.

```
countbit key [offset] [size]
```

- **offset** -  optional, the offset position
- **size** - optional, the size limit to count in.

Return the count result.

```python
>>> ssdb.countbit('key')
8
```

substr
------

Get substring of the string value of a key.

```
substr key [offset] [size]
```

- **offset** -  optional, the offset position
- **size** - optional, the size limit to slice.

Return the substring.

```python
>>> ssdb.set('key', 'hello world')
1
>>> ssdb.substr('key', 6, 10)
'world'
```

strlen
-------

Get a key's string value's length.

```
strlen key
```

Return the length, `0` if key dosent exist.

```python
>>> ssdb.strlen('key')
11
```

keys
-----

List keys within a key's range.

```
keys start end limit
```

- **star**: keys range start, empty string `''` for `-inf`
- **end**: keys range end, empty string `''` for `+inf`
- **limit**: keys count limit

Return the list of keys.

```python
>>> ssdb.keys('k', 'key', 5)
['k1', 'k2', 'key']
```

scan
----

List key-value pairs within a key's range.

```
scan start end limit
```

- **star**: keys range start, empty string `''` for `-inf`
- **end**: keys range end, empty string `''` for `+inf`
- **limit**: keys count limit

Return the list of key, value.

```python
>>> ssdb.scan('k', 'key', 5)
['k1', 'v1', 'k2', 'v2', 'key', 'hello world']
```

rscan
-----

Reverse list key-value pairs within a key's range.

```
rscan start end limit
```

- **star**: keys range start, empty string `''` for `-inf`
- **end**: keys range end, empty string `''` for `+inf`
- **limit**: keys count limit

Return the list of key, value.

```python
>>> ssdb.rscan('z', 'y', 5)
['y', '12']
```

multi_set
---------

Batch set the string values of multiple keys.

```
multi_set key1 value1 key2 value2 ..
```

Return the count of keys set.

```python
>>> ssdb.multi_set('x', 1, 'y', 2, 'z', 3)
3
```

multi_get
---------

Batch get the string values of multiple keys.

```
multi_get key1 key2 ..
```

Return the key-value list.

```python
>>> ssdb.multi_get('x', 'y', 'z')
['x', '1', 'y', '2', 'z', '3']
```

multi_del
---------

Batch delete multiple keys.

```
multi_del key1 key2 ..
```

Return the count of keys deleted.

```python
>>> ssdb.multi_del('x', 'y', 'z')
3
```
