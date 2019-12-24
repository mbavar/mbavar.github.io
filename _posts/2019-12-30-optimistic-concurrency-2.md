---
layout: post
title: Write Contention and Optimistic Concurrency II
subtitle:
published: false
---
In the previous post, we [saw]() how to reduce global contention for a concurrent key-value store and how to use versioning to support *read, modify, write* operation. In this post, we show how to use versioning to get rid of local locks all together.

I was already familiar with optimistic vs pessimistic concurrency in the context of DBs. But recently I became aware of its important in cloud/web setting and this prompted me to the discussion here today.



There are much better (albeit quite more complicated) solutions are possible which provide extra functionalities and gurentees without that much global locking. (Most of these however need to throw away hashmaps with more sophisticated.) The general priciples for optimistic concurrency is as follows, avoiding locks whenever possible or at least reducing it to local locking. Use immutability and versioning instead to achieve atomic behavior over multiple API requests (instead of commits). 


Even though *LCKVStore* is not a fully optimistic implementation, it has some elements of that type of design. 

The problem here is that design is too *pessimistic*. 

## Conclusion

There are a lot of real-world use cases of Optimistic Concurrency. It is used in various powerful popular databses such as  PostgreSQL, MySQL, CockroachDB, etc. 

Outside the world of databases, these ideas are also useful in internet protocols. In particular, when modifying resources via REST API most applications do not consider locking to be a good idea and instead rely on headers such as `if-not-modified-since` to achieve transactional behavior in a more optimistic way. As a real world example you can look at Azure Stroage API [here](https://docs.microsoft.com/en-us/azure/storage/common/storage-concurrency) there. The case of other clouds is also pretty similar. 

Pros:

* more performant in low-contention settings.
* No deadlock possible in fully optimistic implementations, i.e. when there no lock at all.

Cons:

* can be costly to roll back whole transactions when aborting so lower performance in high-contention setting.
* In multi-version world, the old versions need to be garbage collected which may require a stop-the-world operation.


Concurrency is extremely subtle. This turns out to be extremely subtle. Even the most professionals out there such as Golang core contributor sometimes make mistakes when dealing with this (see e.g. the parable of `/dev/null` blackhole here). 

