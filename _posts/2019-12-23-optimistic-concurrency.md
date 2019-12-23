---
layout: post
title: Write Contention and Optimistic Concurrency
subtitle:
show-avatar: false
tags: concurrency, golang
---

The goal of concurrent programming is to organize code in a way that multiple threads of execution can do independent work without losing consistency. When a program is organized in this way, the scheduler can allocate the CPU cycles more efficiently by switching to a different thread when the current one is stuck on IO or a slow system call. This leads to better use of resources and can significantly boost the throughput and latency of an application. There are quite a few interesting topics around concurrency: the different paradigms (such as Go's [share memory by communicating](https://blog.golang.org/share-memory-by-communicating) vs traditional mutex approach), tuning thread counts, common design patterns, green threads, and more. Today, I want to discuss one such topic: ***optimistic concurrency***.


Let's start with an example. Suppose we want to implement the following simple key-value store interface. For simplicity, we do not include `Delete` and `exists` methods, which a full interface should include.

```go
type KVStore interface {
	Get(key string) ([]byte, error)
	Put(key string, val []byte) error
}
```
As this is just the semantics of a HashMap, we can implement it using one. [[1](#design1-footnote)]

```go 
// SimpleKVStore is a simple in-memory implementation of KVStore interface.
type SimpleKVStore struct {
	sync.RWMutex
	data map[string][]byte
}

// Get returns the byte slice associated with the key if it exists.
func (kvs *SimpleKVStore) Get(key string) ([]byte, error) {
	kvs.RLock()
	defer kvs.RUnlock()
	v, ok := kvs.data[key]
	if !ok {
		return nil, fmt.Errorf("Invalid key")
	}
	res := make([]byte, len(v))
	copy(res, v)
	return res, nil
}

// Put associates the value given to the key. After Put returns the val 
// buffer can be modified and reused by the client. 
func (kvs *SimpleKVStore) Put(key string, val []byte) error {
	if val == nil {
		return fmt.Errorf("Nil values are not allowed")
	}
	res := make([]byte, len(val))
	copy(res, val)
	kvs.Lock()
	defer kvs.Unlock()
	kvs.data[key] = res
	return nil
}
```
This is a natural implementation, but also a pretty bad one. The main positive point about it is that it allows concurrent reads. And it is safe and correct! On the negative, it does not support updates to different keys at the same time. The problem is that this *KVStore* is too pessimistic and locks down the whole system for every write. It is natural to wonder if there is a solution with better write contention behavior.

## Sidestepping the global lock (when you can)
The problem is that golang maps (and most other hash maps) do not support concurrent reads/writes. So it seems rather difficult to get rid of this global lock or reduce contention. We can make progress however by relaxing the problem.

Suppose we know that in our application most writes are to the existing keys. This is natural in many applications. For example, if the key-value store holds some data related to each user, in general we'd expect introduction of new users to be rare compared to the changes to the data of the existing ones. To this end, we consider the following modified interface.

```go
type KVStore interface {
	Get(key string) ([]byte, error)
	Put(key string, val []byte) error
	Update(key string, val []byte) error  
}
```
Here, we have added a new method `Update` that can be used to modify the value of an existing key. The hope is that we can reduce the global locking only to `Put` and manage `Update` and `Get` using a read lock. This seems quite paradoxical at first as `Update` still mutates some state, but actually... it is possible! The idea is to have a local lock per value and use pointers.

```go
// LCKVStore is a relatively low-contention key-value store.
type LCKVStore struct {
	sync.RWMutex
	kvMap map[string]*value
}

// value is a value in the LCKVStore.
type value struct {
	sync.RWMutex
	data []byte
}

// Get returns the byte slice associated with the key if it exists.
func (kvs *LCKVStore) Get(key string) ([]byte, error) {
	kvs.RLock()
	defer kvs.RUnlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		return nil, fmt.Errorf("Invalid key")
	}
	val.RLock()
	defer val.RUnlock()
	res := make([]byte, len(val.data))
	copy(res, val.data)
	return res, nil
}

// Update changes the value associated to an existing key.
func (kvs *LCKVStore) Update(key string, v []byte) error {
	kvs.RLock()
	defer kvs.RUnlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		return fmt.Errorf("Invalid key")
	}
	buf := make([]byte, len(v))
	copy(buf, v)
	val.Lock()
	defer val.Unlock()
	val.data = buf
	return nil
}

// Put associates the value given to the key.
func (kvs *LCKVStore) Put(key string, v []byte) error {
	buf := make([]byte, len(v))
	copy(buf, v)
	kvs.Lock()
	defer kvs.Unlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		val = &value{}
	}
	val.data = buf
	kvs.kvMap[key] = val
	return nil
}
```
Here, `Get` and `Put` are basically the same as the previous implementation. What is interesting is what happens in `Update`. The main idea is that even though we are modifying the data in `val` variable, the `kvMap` only has the pointer which is not being modified. Hence, no mutation is happening from the point of view of `kvMap`. At the local level, modifications to the same key are synchronized by a local per value lock.

## Optimistic concurrency
Consider a further requirement for our application to support *read*, *modify*, *write* operation. This is not possible with out current design, as after we read the value and compute the new value based on it, there is no guarantee that a different client hasn't changed the value of the same key. 

One solution is to incorporate transactions in our system.

```go
type TransactionID uuid.UUID

type TxKVStore interface {
	BeginCommit() (TransactionID, error)
	EndCommit(id TransactionID) error 
	Get(key string, id *TransactionID) ([]byte, error)
	Put(key string, val []byte, id *TransactionID) error
	Update(key string, val []byte, id *TransactionID) error
}
``` 
This is a powerful interface, but not an easy one to implement. In particular, obtaining a global lock (even a read lock) during `BeginCommit` without releasing it can be quite dangerous since the client may get delayed or crashes before calling `EndCommit`. Here we focus on a simpler solution that avoids the complexity of full commit semantics while still supporting the *read*, *modify*, *write* operation. The idea is to use *versioning*, which is a main signature of optimistically concurrent systems.

```go
type Version uint64

type OptimisticKVStore interface {
	// Get returns the byte slice associated with the key if 
	// it exists and its version.
	Get(key string) ([]byte, Version, error)
	// Put associates the value given to the key. 
	Put(key string, val []byte) (Version, error)
	// Update changes the value associated to an existing key. 
	// If ver is not nil, it ensures the version matches the 
	// latest one prior to the update. 
	Update(key string, ver *Version) (Version, error)
}
```
The client can use this interface as follows. [[2](#retry-footnote)]
```go
func RMF(store OptimisticKVStore, key string, modify func([]byte) []byte) error {
	data, ver, err := store.Get(key)
	if err != nil {
		return err
	}
	data = modify(data)
	_, err = store.Update(key, data, &ver)
	return err
}
```
The implementation is quite similar  to *LCKVStore* from before. For brevity we only include the `Update` method.

```go 
type value struct {
	sync.RWMutex
	data []byte
	version uint64
}

func (kvs * OptimisticKVStoreImpl) Update(key string, v []byte, ver *Version) (Version, error) {
	kvs.RLock()
	defer kvs.RUnlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		return 0, fmt.Errorf("Invalid key")
	}
	val.Lock()
	defer val.Unlock()
	if ver != nil && *ver != val.version {
		return 0, fmt.Errorf("Stale version")
	}
	buf := make([]byte, len(v))
	copy(buf, v)
	val.data = buf
	val.version += 1
	return val.version, nil
}
```
Note that in most *optimistically concurrent systems*, you would likely need to keep a richer history of each item and their versions (at least for the items involved in an ongoing commit) as well as [write-ahead-logs](https://en.wikipedia.org/wiki/Write-ahead_logging) for rollbacks. It is not surprising that for more complicated applications, you need many more techniques. Hopefully however, the techniques discussed here, versioning and reducing or eliminating the use of heavy handed global locks, have given you a taste of this intricate and interesting topic. 

## Next time
In the next post, I'll discuss the following question: is it possible to go even further in our quest of non-locking and eliminate local locks in *OptimisticKVStoreImpl* above? This will take us to some of the foundational and low-level topics in concurrency. Stay tuned.

### Footnotes
[<a name="design1-footnote">1</a>]: 
There are a few simple design decisions beside the concurrency here. First, by disallowing nil values we have the invariant that the values returned from `Get` are not nil when there is no error, which is a nice guarantee. We could've achieved similar guarantees by using strings, but a more primitive type like byte slice is more appropriate for the generic binary data (such as images) which a KVStore may be storing. 

[<a name="retry-footnote">2</a>]:
This should be called by the client in a retry loop.


