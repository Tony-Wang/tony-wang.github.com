layout: python
title: python中合并字符串的效果
date: 2015-12-02 09:19:53
tags:
    - python
---

基本上所有的动态语言中，都会有这样的技巧，例如lua中可以使用table缓存多行文本，最后合并。

``` python

In [8]: s="aaaaaaaaaaaaaaaaaaaa"

In [9]: l=[s for i in range(1000)]

In [10]: def concat():
   ....:     sb=""
   ....:     for i in l:
   ....:         sb+=i
   ....:     return sb
   ....: 

In [11]: def join():
   ....:     return "".join(l)
   ....: 

In [12]: %timeit concat()
10000 loops, best of 3: 65.4 µs per loop

In [13]: %timeit join()
100000 loops, best of 3: 9.92 µs per loop

```
