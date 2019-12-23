---
layout: post
title: Write Contention and Optimistic Concurrency II
subtitle:
published: false
---


Concurrency is extremely subtle. This turns out to be extremely subtle. Even the most professionals out there such as Golang core contributor sometimes make mistakes when dealing with this (see e.g. the parable of `/dev/null` blackhole here). 

I was already familiar with optimistic vs pessimistic concurrency in the context of DBs. But recently I became aware of its important in cloud/web setting and this prompted me to the discussion here today.



There are much better (albeit quite more complicated) solutions are possible which provide extra functionalities and gurentees without that much global locking. (Most of these however need to throw away hashmaps with more sophisticated.) The general priciples for optimistic concurrency is as follows, avoiding locks whenever possible or at least reducing it to local locking. Use immutability and versioning instead to achieve atomic behavior over multiple API requests (instead of commits). 


Even though *LCKVStore* is not a fully optimistic implementation, it has some elements of that type of design. 

The problem here is that design is too *pessimistic*. 


Pros:

* more performant in low-contention settings.
* no deadlock possible

Cons:

* can be costly to roll back whole transactions when aborting so lower performance in high-contention setting.


This post was inspired by some work I've been doing around Azure Storage and the Concurrency management system [there](https://docs.microsoft.com/en-us/azure/storage/common/storage-concurrency). The case of other clouds is also pretty similar. 


So I thought as the inagurual real *technical* post in this blog, it'd be great to place to start. 

So basically start with KV store with PUT and GET.

Suppose we know most of PUTs are UPDATEs, we want to have high write throughput. 

Now suppose we want to have REad, Modify, Write behavior on top of this. 

Can we avoid locking completely?
