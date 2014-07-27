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
- [hget](#hget)
- [hset](#hset)
- [hdel](#hdel)
- [hincr](#hincr)
- [hexists](#hexists)
- [hsize](#hsize)
- [hlist](#hlist)
- [hkeys](#hkeys)
- [hgetall](#hgetall)
- [hscan](#hscan)
- [hrscan](#hrscan)
- [hclear](#hclear)
- [multi_hset](#multi_hset)
- [multi_hget](#multi_hget)
- [multi_hdel](#multi_hdel)
- [zset](#zset)
- [zget](#zget)
- [zdel](#zdel)
- [zincr](#zincr)
- [zsize](#zsize)
- [zexists](#zexists)
- [zlist](#zlist)
- [zkeys](#zkeys)

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

- If key dosent exist, set this key to `0` and increment its value by `number`.
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

hset
----

Set the string value of a hash field.

```
hset hash field value
```

Return `1` if new hashmap or new field was created, else `0`, both `0` and `1`
for success set.

```python
>>> ssdb.hset('key', 'field', 'val')
1
>>> ssdb.hset('key', 'field', 'val2')
0
```

hget
----

Get the string value of a hash field.

```
hget hash field
```

Return the string value, `null` if `field` or `hash` dosent exist.

```python
>>> ssdb.hget('hash', 'field')
'val2'
>>> ssdb.hget('hash', 'field-not-exist') is None
True
>>> ssdb.hget('hash-not-exist', 'field') is None
True
```

hdel
----

Delete a hash field.

```
hdel hash field
```

Return 1 for success, `0` if `field` or `hash` dosent exist.

```python
>>> ssdb.hdel('hash', 'field')
1
```

hincr
-----

Increment the integer value of a hash field by the given number.

```
hincr hash field number
```

Return the value after the increment operation.

- If `hash` dosent exist, set this `hash` a hashmap, and set its field `field` to `0`, then do increment operation.
- If the `field` dosnt exist, set it to `0`, then do increment operation.
- If the value isnt a numberic string, cast it to integer and do increment operation.

```python
>>> ssdb.hincr('hash', 'field', 2)
3
```

hexists
-------

Determine if a hash field exists.

```
hexists hash field
```

Return `1` if the `hash` and `field` both exist, else `0`.

```
>>> ssdb.hexists('hash', 'field')
True
```

hsize
-----

Get a hashmap's size.

```
hsize hash
```

Return the size if `hash` exists, else return `0`.

```python
>>> ssdb.hsize('hash')
1
```

hlist
-----

List hashmaps within a key range.

```
hlist start end limit
```

- **star**: hashmap range start, empty string `''` for `-inf`
- **end**: hashmap range end, empty string `''` for `+inf`
- **limit**: hashmap count limit

Return the hashmap list.

```python
>>> ssdb.hlist('', '', 1)
['hash']
```

hkeys
-----

List fields in a hashmap within a field range.

```
hkeys start end limit
```
- **star**: fields range start, empty string `''` for `-inf`
- **end**: fields range end, empty string `''` for `+inf`
- **limit**: fields count limit

Return the fields list.

```python
>>> ssdb.hkeys('hashmap', '', '', 1)
['field']
```

hgetall
-------

List all field-value pairs in a hashmap.

```
hgetall hash
```

Return field and value list

```python
>>> ssdb.hgetall('hash')
['k', 'v', 'key', 'val']
```

hscan
-----

List field-value pairs within a field range.

```
hscan hash start end limit
```

- **star**: fields range start, empty string `''` for `-inf`
- **end**: fields range end, empty string `''` for `+inf`
- **limit**: fields count limit

Return field and value list.

```python
>>> ssdb.hscan('hash', '', '', 2)
['k', 'v', 'key', 'val']
```

hrscan
------

Reverse list field-value pairs within a field range.

```
hrscan hash start end limit
```

- **star**: fields range start, empty string `''` for `-inf`
- **end**: fields range end, empty string `''` for `+inf`
- **limit**: fields count limit

Return field and value list.

```python
>>> ssdb.hrscan('hash', '', '', 2)
['key', 'val', 'k', 'v']
```

hclear
------

Clear a hashmap.

```
hclear hash
```

Return the count of fields deleted.

```python
>>> ssdb.hclear('hash')
2
```

multi_hset
----------

Batch set multiple fields in a hashmap.

```
multi_hset hash field value ..
```

Return the count of fields created, but non-negative integer for success.

```python
>>> ssdb.multi_hset('hash', 'k1', 'v1', 'k2', 'v2')
2
```

multi_hget
----------

Batch get the string values of multiple fields in a hashmap.


```
multi_hget hash field ..
```

Return the values list.

```python
>>> ssdb.multi_hget('hash', 'k1', 'k2')
['k1', 'v1', 'k2', 'v2']
```

multi_hdel
----------

Batch delete multiple fields in a hashmap.


```
multi_hdel hash field ..
```

```python
>>> ssdb.multi_hdel('hash', 'k1', 'k2')
2
```

zset
----

Set the score value of a key in a zset.

```
zset zset key score
```

Return 1 if a new key was created, else 0.

```python
>>> ssdb.zset('zset', 'key', 4)
1
```

zget
----

Get the score value of a key in a zset.

```
zget zset key
```

Return the key's score value.

```python
>>> ssdb.zget('zset', 'key')
4
```

zdel
----

Delete a key in a zset.

```
zdel zset key
```

Return `1` on success, `0` if `key` was not found.

```python
>>> ssdb.zdel('zset', 'key')
1
```

zincr
-----

Increment the score value of a key by the given number in a zset.

```
zincr zset key number
```

```python
>>> ssdb.zincr('zset', 'key', 2)
6
```

Return the value after the increment operation.

- If zset dosent exist, create this zset , set the key with socre `0`, then do increment operation.
- If key dosent exist, set the key with score `0` and then do increment operation.
- If the value isnt a numberic string, cast it to integer and do increment operation.

```python
>>> ssdb.zincr('zset', 'key', 2)
6
```

zexists
-------

Determine whether a key exists in a zset.

```
zexists zset key
```

Return 1 if the key exists, else `0`.

```python
>>> ssdb.zexists('zset', 'key')
True
```


zsize
-----

Get a zset's size.


```
zsize zset
```

Return the count, if `zset` dosent exist, return `0`.

```python
>>> ssdb.zsize('zset')
2
```

zlist
------

List zsets within a name range.

```
zlist start end
```

- **star**: zset name range start, empty string `''` for `-inf`
- **end**: zset name range end, empty string `''` for `+inf`
- **limit**: zset count limit

Return the list of zsets.

```python
>>> ssdb.zlist('', '', 100)
['z', 'zset']
```

zkeys
-----


