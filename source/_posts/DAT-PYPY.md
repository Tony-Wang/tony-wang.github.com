title: python实现的DAT字典多匹配效率试验
date: 2015-09-23 09:51:13
tags: 
    - 中文分词
    - python
---
从HanLP看到了[darts-java](https://github.com/komiya-atsushi/darts-java),话说小日本除了苍老师还贡献了这样精巧的代码。
用python实现了双数组trie分词,一直担心效率问题。看到hancs的[《DoubleArrayTrie和AhoCorasickDoubleArrayTrie的实用性对比》](http://www.hankcs.com/program/algorithm/double-array-trie-vs-aho-corasick-double-array-trie.html)这篇，很有兴趣，立刻依样画葫芦，试验了一把。
```
# -*- coding:utf-8 -*-
import codecs
import time
from yaya.collection.dict import DoubleArrayTrie
__author__ = 'tony'


def main():
    f = codecs.open("./data/我的团长我的团.txt", 'r', "utf-8")
    text = f.read()
    search = DoubleArrayTrie.searcher(text)
    i = 0
    start = time.time()
    while search.next():
        i += 1
        pass

    print "分词时间: %s" % (time.time() - start)
    print "分词个数: %s" % i
    print "词典大小: %s" % search.trie.keySize
    print "文本大小: %s" % len(text)

if __name__ == '__main__':
    main()

```

ubuntu 14.04 64位
python 2.7.4
Intel(R) Core(TM) i5-2400 CPU @ 3.10GHz
12G内存

测试结果不出所料地让我崩溃,HanLP的实验都是毫秒级，都不超过50毫秒。

```

/usr/bin/python /home/tony/MyProject/YaYaNLP/test/DemoDATMulitMatch.py
分词时间: 1.75682616234
分词个数: 945685
词典大小: 153117
文本大小: 815841

Process finished with exit code 0

```

换成pypy试试，却给了我信心。

pypy 2.2.1

```

/usr/lib/pypy/bin/pypy-c /home/tony/MyProject/YaYaNLP/test/DemoDATMulitMatch.py
分词时间: 0.190576076508
分词个数: 945685
词典大小: 153117
文本大小: 815841

Process finished with exit code 0

```

吼吼，效率提升很多。




