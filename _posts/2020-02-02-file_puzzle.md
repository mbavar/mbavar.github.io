---
layout: post
title: "A Puzzle about Filesystems"
subtitle: 
show-avatar: false
---

 There are many possible explanations for why designing good APIs is hard. Here are three that come to my mind: 
 
* Developers are bad at sympathy and don't understand the users.
* Users are lazy, don't read the documentations, and misuse the APIs.
* It's genuinely hard to balance between abstracting out the details and providing flexibility. 

One upshot of these issues is that people usually end up designing simple-looking APIs that hide some nasty corner cases. And users typically end up not really facing the corner cases and go happily about their lives. That is, of course, until they either chance upon such cases in a nasty bug, or when the said users try to design a similar API. And then, they realize they don't know how to deal with some cases and start looking for inspirations in familiar APIs!

This exact scenario happened to me a few months ago. I was designing an API that had somewhat similar semantic to a filesystem. Then I realized, to my surprise, that there are corner cases in filesystem API that I didn't fully understand. 

## Puzzle
To get an idea of my case, consider the following Python program. 

```python
import os


def filesystem_puzzle(filename):
    # create file
    with open(filename, 'w') as f:
        f.write('When in the course ')

    # open the file again
    f = open(filename, "r+")
    print(f.read())
    f.write('of human events, ')
    os.remove(filename) # remove the file but continue read & write
    f.write('it becomes necessary...')
    f.seek(0)
    print(f.read())
    f.close()

    if os.path.exists(filename):
        with open(filename,'r') as f:
            print(f.read())
    else:
        print(f'{filename} not found')


filename = './post.txt'
filesystem_puzzle(filename)
```
Even though you might be well-versed in manipulating files, the above program might still show that there are some blindspots in your knowledge. The issue here is the `os.remove` and the subsequent write and read; should `os.remove` even work when the file is open? Should the subsequent read/write throw an exception?

As you can expect, this conundrum may happen in many forms of API design. There are often calls to create/remove a resource entirely as well as calls to manipulate the resource incrementally (e.g. write/read here), and it can be tricky to design their interactions.

In the case of filesystems and the above program, it turns out the above is safe and does not throw any exception. The output is the following:

```
When in the course 
When in the course of human events, it becomes necessary...
./post.txt not found
```
The key to understanding this is to consider the point of view of the API designer. What should `os.remove` do? Should it go and laboriously delete all blocks associated to a file from disk? Upon reflection, it is clear that's not such a good idea: it is slow, unnecessary, and as the above program demonstrates, error-prone (especially if you note that the `os.remove` could have been called concurrently from a different process or thread). Only thing that `os.remove` does is to remove the link from the current directory to `post.txt` inode. It does not touch the open file descriptor `f` that is being written to. This is confirmed by looking up `unlink` system call [documentation](https://linux.die.net/man/2/unlink), which also explains how the space is reclaimed from disk eventually.

_unlink() deletes a name from the filesystem.  If that name was the
last link to a file and no processes have the file open, the file is
deleted and the space it was using is made available for reuse. If the name was the last link to a file but any processes still have
the file open, the file will remain in existence until the last file
descriptor referring to it is closed._


I hope you found this puzzle interesting! Until next time.





