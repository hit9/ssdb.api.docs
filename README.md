SSDB API Docs
=============

Prewords
--------

This is an unofficial documentation for [ssdb](https://github.com/ideawu/ssdb),
which is a fast nosql database with a variety of data structures,
welcome to fork and send me pull requests.

#### examples

All examples are in Python, via
[ssdb.py](https://github.com/hit9/ssdb.py).

#### parameters

- parameters like `limit`, `-1` for all.
- parameters like `start`, `''` for `-inf`.
- parameters like `end`, `''` for `+inf`.


- for interfaces like `*keys`, `*scan` and `*list`, the range section is left open and right closed.

#### responses

Ssdb responses follows this pattern:

```
status [value]
```

Status maybe `"ok"`, `"not_found"` or `"client_error"` etc.


#### commands

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
- [hrlist](#hrlist)
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
- [zrlist](#zrlist)
- [zkeys](#zkeys)
- [zscan](#zscan)
- [zrscan](#zrscan)
- [zrank](#zrank)
- [zrrank](#zrrank)
- [zrange](#zrange)
- [zrrange](#zrrange)
- [zclear](#zclear)
- [zcount](#zcount)
- [zsum](#zsum)
- [zavg](#zavg)
- [zremrangebyrank](#zremrangebyrank)
- [zremrangebyscore](#zremrangebyscore)
- [multi_zset](#multi_zset)
- [multi_zget](#multi_zget)
- [multi_zdel](#multi_zdel)
- [qsize](#qsize)
- [qclear](#qclear)
- [qfront](#qfront)
- [qback](#qback)
- [qget](#qget)
- [qslice](#qslice)
- [qpush](#qpush)
- [qpush_front](#qpush_front)
- [qpush_back](#qpush_back)
- [qpop](#qpop)
- [qpop_front](#qpop_front)
- [qpop_back](#qpop_back)
- [qlist](#qlist)
- [qrlist](#qrlist)

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
- **section**: `(start, end]`

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
- **section**: `(start, end]`

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
- **section**: `(start, end]`

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
- **section**: `(start, end]`

Return the hashmap list.

```python
>>> ssdb.hlist('', '', 1)
['hash']
```

hrlist
------

Reverse list hashmaps within a key range.

```
hrlist start end limit
```

see also [hlist](#hlist).

hkeys
-----

List fields in a hashmap within a field range.

```
hkeys start end limit
```
- **star**: fields range start, empty string `''` for `-inf`
- **end**: fields range end, empty string `''` for `+inf`
- **limit**: fields count limit
- **section**: `(start, end]`

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
- **section**: `(start, end]`.

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
- **section**: `(start, end]`.

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
zlist start end limit
```

- **star**: zset name range start, empty string `''` for `-inf`
- **end**: zset name range end, empty string `''` for `+inf`
- **limit**: zset count limit
- **section**: `(start, end]`.

Return the list of zsets.

```python
>>> ssdb.zlist('', '', 100)
['z', 'zset']
```

zrlist
------

Reverse list zsets within a name range.

```
zrlist start end
```

see also [zrlist](#zrlist).

zkeys
-----

List keys within some range in a zset.

```
zkeys zset key_start score_start score_end limit
```

The range is defined like:

```
(key.score == score_start && key > key_start) || (key.score > score_start)
```

- **key_start**: zset key range start, empty string `""` for `-inf`
- **score_start**: zset score range start, empty string `""` for `-inf`
- **score_end**: zset score end, empty string `"" for `+inf
- **limit**: zset keys count limit
- **section**: left open and right closed.

Return the list of keys.

```python
>>> ssdb.zkeys('zset', '', '', '', -1)  # list all keys
['keyf', 'key', 'key1', 'key2']
>>> ssdb.zkeys('zset', '', 10, '', -1)  # list all keys with score not letter than 10
['key1', 'key2']
```

zscan
-----

List key-score pairs within some range in a zset.

For the range and parameters, see [zkeys](#zscan)

```python
>>> ssdb.zscan('zset', '', '', '', -1)  # list all key-score pairs
['keyf', '3', 'key', '6', 'key1', '10', 'key2', '30']
>>> ssdb.zscan('zset', '', 10, '', -1)  # list all key-score pairs with score not letter than 10
['key1', '10', 'key2', '30']
```

zrscan
-------

Reverse list key-score pairs within some range in a zset, see also
[zscan](#zscan).

```python
>>> ssdb.zrscan('zset', '', '', '', -1)  # reverse list all key-score pairs
['key2', '30', 'key1', '10', 'key', '6', 'keyf', '3']
```

zrank
-----

Get the rank of a key sorted by scores in a zset.

*Note: this method is very slow, try to not use it in production*.

```
zrank zset key
```

Return the rank, `-1` if key wasnt found in the zset.

```
>>> ssdb.zrank('zset', 'key')
1
>>> ssdb.zrank('zset', 'key-not-exist')
-1
```

zrrank
------

Get the rank of a key reverse sorted by scores in a zset, see also
[zrank](#zrank)

```python
>>> ssdb.zrrank('zset', 'key')
2
>>> ssdb.zrrank('zset', 'key-not-exist')
-1
```

zrange
------

Get key-score pairs sorted by scores in a zset.

```
zrange zset offset limit
```

- **offset**: the poisition to start. *offset bigger, zrange slower*
- **limit**: keys returned count limit, `-1` for all.
- **section**: `[offset, offse + limit)`.

```python
>>> ssdb.zrange('zset', 0, -1)  # Get all key-score pairs in order, sorted by scores
['keyf', '3', 'key', '6', 'key1', '10', 'key2', '30']
```


zrrange
-------

Get key-score pairs reverse sorted by scores in a zsetm, see also
[zrange](#zrange).

```
zrrange zset offset limit
```

```python
>>> ssdb.zrrange('zset', 0, -1)  # Get all key-score pairs in order, reverse sorted by scores
['key2', '30', 'key1', '10', 'key', '6', 'keyf', '3']
```

zclear
------

Clear a zset.

```
zclear zset
```

Return the count of keys deleted

```python
>>> ssdb.zclear('zset')
4
```

zcount
------

Count keys with score in a range.

```
zcount zset score_start score_end
```

- **section**: `[score_start, score_end]`.

Return the count.

```python
>>> ssdb.zcount('zset', 0, 50)
2
```

zsum
----

Sum the scores with score in a range.

```
zsum zset score_start zscore_end
```

- **section**: `[score_start, score_end]`.

Return the sumed value.

```python
>>> ssdb.zsum('zset', '', '')  # sum all scores
130
```

zavg
----

Caculate the average value of scores with score in a range.


```
zavg zset score_start zscore_end
```

- **section**: `[score_start, score_end]`.

```python
>>> ssdb.zavg('zset', '', '')
43.333333
```

zremrangebyrank
---------------

Remove all members in a sorted set within the given indexes.

```
zremrangebyrank zset rank_start rank_end
```

- **section**: `[rank_start, rank_end]`.

Return the count of members removed.

```python
>>> ssdb.zremrangebyrank('zset', 0, 1)
2
```

zremrangebyscore
----------------

Remove all members in a sorted set within the given score range.

```
zremrangebyscore score_start score_end
```

- **section**: `[score_start, score_end]`.

Return the count of members removed.

```python
>>> ssdb.zremrangebyscore('zset', 10, 20)
1
```

multi_zset
----------

Batch set the score value of multiple keys in a sorted set.

```
multi_zset zset key1 score1 ..
```

Return the count of keys created.

```python
>>> ssdb.multi_zset('zset', 'k1', 10, 'k2', 100)
2
```

multi_zget
----------

Batch get the score value of multiple keys in a sorted set.

```
multi_zget zset key1 key2 ..
```

Return the list of scores.

```python
>>> ssdb.multi_zget('zset', 'k1', 'k2')
['k1', '10', 'k2', '100']
```

multi_zdel
----------

Batch remove members by multiple keys.

```
multi_del zset key1 key2 ..
```

Return the count of members removed.

```python
>>> ssdb.multi_zdel('zset', 'k1', 'k2')
2
```

qsize
-----

Get the size of a queue.

```
qsize queue
```

Return queue's size.

```python
>>> ssdb.qsize('q')
100
```

qclear
------

Clear a queue.

```
qclear queue
```

Return the count of members removed.

```python
>>> ssdb.qclear('q')
100
```

qfront
------

Get the first member of a queue.

```
qfront queue
```

Return the first member, `null` on an empty queue.

```python
>>> ssdb.qfront('q')
'0'
```

qback
-----

Get the last memver of a queue.

```
qback queue
```

Return the last member, `null` on an empty queue.

```python
>>> ssdb.qback('q')
'99'
```

qget
----

Get member in a queue by given index.

```
qget queue index
```

Return the member, `null` if the member was not found.

```python
>>> ssdb.qget('q', 10)
'10'
```

qslice
------

Get a range of members from a queue.

```
qslice queue start end
```

- **section**: `[start, end]`.

Return the list of members.

```python
>>> ssdb.qslice('q', 10, 15)
['10', '11', '12', '13', '14', '15']
```

qpush
------

Alias for [qpush_back](#qpush_back).

qpush_front
-----------

Insert a member to the head of a queue.

```
qpush_front queue member
```

Return the queue's size after push operation.

```python
>>> ssdb.qpush_front('q', 34)
101
```

qpush_back
----------

Append a member to the end of a queue.

```
qpush_back queue member
```

Return the queue's size after push operation

```python
>>> ssdb.qpush_back('q', 34)
102
```

qpop
----

Alias for [qpop_front](#qpop_front).

qpop_front
----------

Pop a member from the head of a queue.

```
qpop_front queue
```

Return the poped member, `null` if the queue dosent exist or is empty.

```python
>>> ssdb.qpop_front('q')
'34'
```

qpop_back
---------

Pop a member from the end of a queue.

```
qpop_back queue
```

Return the poped member, `null` if the queue desent exist or is empty.

```python
>>> ssdb.qpop_back('q')
'99'
```

qlist
-----

List all queues within a name range.

```
qlist start end
```
-
- **star**: queue name range start, empty string `''` for `-inf`
- **end**: queue name range end, empty string `''` for `+inf`
- **limit**: queues count limit

Return the list of queues names.

```python
>>> c.qlist('', '', 1)
['q']
```

qrlist
------

Reverse list all queues within a name range.

see also [qlist](#qlist).
