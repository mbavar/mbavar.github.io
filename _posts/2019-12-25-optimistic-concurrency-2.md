---
layout: post
title: Write Contention and Optimistic Concurrency II
subtitle:
show-avatar: false
# published: false
---
In the last [post](https://bavarian.dev/blog/optimistic-concurrency/), we saw how to reduce global contention in a concurrent key-value store and provide some limited transactional support through versioning. In this post, we show how to use versioning to get rid of local locks. The key here is the *compare-and-swap* [instruction](https://en.wikipedia.org/wiki/Compare-and-swap). This is the atomic version of the following:

```go
func CompareAndSwapUint64(addr *int64, old, new int64) (swapped bool) {
	if *addr == old {
		*addr = new
		return true
	}
	return false
}
```
Atomic [compare-and-swaps](https://golang.org/pkg/sync/atomic/) are purely user space and unlike mutexes would never require any help from the operating system. Hence, they can be much cheaper. The tradeoff is that we have to allow `Get` or `Put` fail sometimes in the case of simultaneous reads/writes, in which case the client needs to retry. This is captured by the `ConflictErr` variable below.

```go
type Version uint64

var ConflictErr = fmt.Errorf("Conflict detected, retry")
var StaleVersionErr = fmt.Errorf("Stale version detected")

type value struct {
	data    []byte
	version *uint64
}

// CSKVStore is a low level key-value store that uses atomic operations 
// instead of local locks. It still uses a global lock for coordinating 
// insertion of new keys.
type CSKVStore struct {
	sync.RWMutex
	kvMap map[string]*value
}
```
This is similar to the versioned *LCKVStore* from the last [post](https://bavarian.dev/blog/optimistic-concurrency/) except now the version appears as a pointer. This is necessary because of the *compare-and-swap* signature. 

Before going into the details, we should mention a few points about our design. First, all access to the actual value of the version is always through atomic operations. Second, we have the following invariant: both `Get` and `Put` will only return even version values. The odd values are temporary states used internally in the store to indicate the presence of a concurrent write operation and are never exposed to the client.
{% highlight go linenos %}
// Get returns the byte slice associated with the key if it exists. 
func (kvs *CSKVStore) Get(key string) ([]byte, Version, error) {
	kvs.RLock()
	defer kvs.RUnlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		return nil, 0, fmt.Errorf("Invalid key")
	}
	verp := val.version
	ver := atomic.LoadUint64(verp)
	if ver % 2 != 0 {
		return nil, 0, ConflictErr
	}
	res := make([]byte, len(val.data))
	copy(res, val.data)
	if atomic.CompareAndSwapUint64(verp, ver, ver) {
		return res, Version(ver), nil
	}
	return nil, 0, ConflictErr
}
{% endhighlight %}

The main things to note here are lines 10 and 16. These make sure that if there is any write to this key while we are copying data, the read will not go through. To get a more full picture, we should take a look at the `Put` implementation.
{% highlight go linenos %}
// Put associates the value with the given key. When ver is not 
// nil, it updates only if the given version matches the current one. 
// When using non-nil ver, the client must provide a version received 
// from a previous successful Put or Get. Any other version may 
// result in an unexpected behavior.
func (kvs *CSKVStore) Put(key string, ver *Version, v []byte) (Version, error) {
	kvs.RLock()
	val, ok := kvs.kvMap[key]
	if !ok {
		kvs.RUnlock()
		return kvs.createOrUpdate(key, v)
	}
	buf := make([]byte, len(v))
	copy(buf, v)
	var expected uint64
	verp := val.version
	if ver != nil {
		expected = uint64(*ver)
	} else {
		expected = atomic.LoadUint64(verp)
	}
	if expected%2 != 0 {
		if ver != nil {
			return 0, fmt.Errorf("Invalid version")
		}
		return 0, ConflictErr
	}
	if atomic.CompareAndSwapUint64(verp, expected, expected+1) {
		val.data = buf
		atomic.StoreUint64(verp, expected+2)
		return Version(expected + 2), nil
	}
	if ver != nil {
		return 0, StaleVersionErr
	}
	return 0, ConflictErr
}

// createOrUpdate associates the value to the key. It works whether
// the key is existing or not. It acquires an exclusive lock.
func (kvs *CSKVStore) createOrUpdate(key string, v []byte) (Version, error) {
	buf := make([]byte, len(v))
	copy(buf, v)
	kvs.Lock()
	defer kvs.Unlock()
	val, ok := kvs.kvMap[key]
	if !ok {
		var version uint64
		val = &value{version: &version}
	}
	val.data = buf
	kvs.kvMap[key] = val
	return Version(*val.version), nil
}
{% endhighlight %}
The above code is somewhat subtle. The key to understanding it is to realize that we are implicitly using the parity of the version as a local lock. A successful write increases the version by 2 (line 30), while an unsuccessful does not change it. In more detail, note that the only times the version is odd are after lines 28 & 29. This is the critical section of the code. For example, suppose some thread A has reached and stopped in the middle of this section. If another thread B computes `expected` version (line 18 or 20) and tries to enter the critical section, it will fail either in line 22 or 28, due to either staleness of its version (since it has read the version before A had increased it in line 28) or because of its parity (in case it has read the version after A made it odd in line 28). This ensures the integrity of writes.

## Tradeoffs

The above low level key-value store highlights another design principle in optimistic concurrency: ***sacrificing 100% rate success for performance***. But what are the cons of this design? Beside complexity, which in this case was due to our reliance on low-level libraries and not inherent to the optimistic design, two other considerations are *starvation and fairness*. 

These issues often do not arise in low-contention settings but can be major problems otherwise. An advantage of lock based designs is that starvation can be mitigated by employing a decent scheduler. However, in optimistic systems the responsibility of retrying in the case of temporary errors falls to the client, and it may not be clear at what frequency the client should retry, or if there is any guarantee from its perspective that it won't be starved in favor of more aggressive clients. These issues can be addressed partially with more careful designs and the help of rate limiters in real world settings. 

## Conclusion

The ideas we have been discussing about optimistic systems are not just theory. They are for example used often in some of the most powerful popular databases such as [PostgreSQL](https://www.postgresql.org/docs/9.1/mvcc-intro.html), [MySQL](https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html), [CockroachDB](https://www.cockroachlabs.com/docs/stable/architecture/storage-layer.html#mvcc), etc. Outside the world of databases, these ideas are useful in the internet protocols. In particular, when modifying resources via a REST API, most applications do not consider locking to be a good idea and instead rely on headers such as `if-not-modified-since` to achieve transactional behavior in an optimistic way.

Finally, we note that concurrency is a subtle subject. Even the most seasoned professionals sometimes make mistakes using it. Hence, it's important not to be lured by it when your application does not actually require or benefit much from it. But once these powerful methods become important and you start on the quest of designing your concurrent system, have in mind optimistic design principles. And always do extensive testing.

And that's it for this series!
