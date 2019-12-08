---
layout: post
title: Optimistic vs pessimistic concurrency
subtitle:
published: false
---

Concurrent programming is one of my favorite topics in systems programming so as the inagurual post in this blog, I thought it'd be great to place to start. 

The goal of concurrent programming is to organize software in a way that a) multiple threads of execution can do independent work at the same time and b) to do so in a way the results remain consistent without apriori knowledge about the order of execution of the threads in runtime. As you can imagine, when a program is organized in this way, the scheduler can allocate the CPU cycles more efficiently by switching to another thread while the current one is busy waiting on an slow memory or network request, and hence improve the overall latency and throughput of an application. There are quite a variety of topics to cover about concurrency: How to write programs in this way, what algorithms to use for the scheduler, or how to tune the number of threads or even when is it worth to use concurrency at all (a lot of times you are better off with a single thread than pay the context switching overhead). Today, I discuss one such topic that may be less widely known: *optimistic* vs *pessimistic* concurrency.


Let us first go over pessimistic concurrency as this may the more familiar paradigm. Suppose we want to implement the following interface.

```
type WordCounter interface {
	// Add a new document whose word count must be added to the total counts.
	AddDoc(fileName string) (done <- chan struct{}, err error)
	// Get the frequency of a word in the whole corpus.
	GetFreq(word string) float64
	// Get count of a word in the whole corpus.
	GetCount(word string) int
}
```
As the name suggests `WordCounter` role is to keep the word counts of a growing body of documents. Since documents may be large and take long to process we would like the `AddDoc` method to be asynchronous and does not completely block our read operations, i.e. we want to `GetFreq` and `GetCount` to be as available as possible.



This is paramount to multi-threaded or multi-process programming and as a result to any distributed systems environment. So far this must be familiar to most of developers out there. The basic paradigm of concurrent programming is through locks and condition variables.
It's fair to assume that most developers, whether just starting their career or in high position in industry or academia, have a passing familiarity with concurrent programming. 

I was already familiar with optimistic vs pessimistic concurrency in the context of DBs. But recently I became aware of its important in cloud/web setting and this prompted me to the discussion here today.

Pros:

* more performant in low-contention settings.
* no deadlock possible

Cons:

* can be costly to roll back whole transactions when aborting so lower performance in high-contention setting.


This post was inspired by some work I've been doing around Azure Storage and the Concurrency management system [there](https://docs.microsoft.com/en-us/azure/storage/common/storage-concurrency). The case of other clouds is also pretty similar. 