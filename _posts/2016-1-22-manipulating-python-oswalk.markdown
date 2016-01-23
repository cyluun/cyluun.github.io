---
layout: post
title:  "Manipulating Python's os.walk()"
date:   2016-1-23
description:  How do I use os.walk()? Can I tweak os.walk() in order to produce a customized tree? How can I exclude hidden directories from os.walk()?
---

Python's os.walk() is a method that walks a directory tree, yielding lists of directory names and file names. If you're not close friends though, it can appear tricky to control. It may flood your screen with hidden files or generally have poor boundaries! This is my effort for you guys to get to know each other a bit better.

##How do I use os.walk()?

A standard way of walking down a *path* with os.walk() is making a loop:

{% highlight python %}
import os

path = './Documents'

for root, dirs, files in os.walk(path):
    print root, dirs, files
{% endhighlight %}


Example output:
{% highlight bash %}
./Documents ['other', 'Projects'] ['text.txt']
./Documents/other [] ['game.cpp']
./Documents/Projects [] ['graph.py', 'walk.py', 'sort.py']
{% endhighlight %}


Try it out in your python REPL in order to get the gist of it. It will print out the root directory path for each loop, a list of the dirs in it and a list of the files in it.

Another way of doing it would be by using the **os.path.join()** method, which will print the full paths of the directories and files.

{% highlight python %}
import os

path = './Documents'

for root, dirs, files in os.walk(path):
    print root
    for dir in dirs:
        print os.path.join(root, dir)
    for file in files:
        print os.path.join(root, file)
{% endhighlight %}

Which will result in a clean print:
{% highlight bash %}
./Documents
./Documents/other
./Documents/Projects
./Documents/text.txt
./Documents/other
./Documents/other/game.cpp
./Documents/Projects
./Documents/Projects/graph.py
./Documents/Projects/walk.py
./Documents/Projects/sort.py
{% endhighlight %}


##But how can I tweak os.walk() in order to produce a customized tree?

What if you want to:

* Not go into hidden directories

* Not list a certain type of files

* Not go into directories that match a path pattern

The plain os.walk() loop can seem like an unstoppable force of nature once provided with a path. It will go through every directory and every file until it can't go no more! [Python's docs][walk] are hinting a solution for that, but let's make it more comprehensible.


##How can I exclude hidden directories from os.walk()?

The answer here lies in making a copy of the `dirs` list and filtering the items.


{% highlight python %}
import os

path = './Documents'

for root, dirs, files in os.walk(path):
    print root

    dirs[:] = [d for d in dirs if not d.startswith('.')]

    for dir in dirs:
        print os.path.join(root, dir)
    for file in files:
        print os.path.join(root, file)
{% endhighlight %}

With a list comprehension, now our list of directories does not include hidden directories. Moreover, os.walk() won't go into those directories at all. We can do the same with the `files` list.

##How can I exclude other specific directories?

For example, you may have another list of directory names that you want to ignore during your os.walk(). One way to do this would be the same as above, with a list comprehension. Another way of doing it would be to check the `root` each time, and in case it's in the ignore list, empty both the `dirs` and the `files` list.

{% highlight python %}
for root, dirs, files in os.walk(path):
    if root in ignore_list:
        dirs[:] = []
        files[:] = []
{% endhighlight %}

This kind of way will serve you better if you're not working with an ignore list, but a **path pattern**. Since a list comprehension would not work with a pattern, you have to check if the `root` matches the pattern each time. You can use `fnmatch` or `glob` for that.

And with that one, we conclude our little get-together with os.walk(). And remember, it's all in the lists!


[walk]: https://docs.python.org/2/library/os.html#os.walk
