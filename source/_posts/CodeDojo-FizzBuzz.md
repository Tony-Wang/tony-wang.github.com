title: '编程道场:FizzBuzz'
tags:
  - Dojo
  - FizzBuzz
  - Kata
  - Python
  - TDD
  - 编程道场
  - 招式
categories: []
date: 2016-05-23 13:28:00
---
### Code Dojo:FizzBuzz Kata by python
### 使用TDD解决FizzBuzz问题

![](http://7xpa6t.com1.z0.glb.clouddn.com/16-6-1/87882963.jpg)

　　这是和一群小伙伴玩编程道场，练习一个TDD的KATA时的小题目，这样的形式很有意思，大家的关注力从头到尾都很集中。

> 问题描述：
小朋友玩的一个有意思的游戏，每个人按顺序报数，如果遇到３或者３的倍数则喊：“Fizz”，如果遇到５或者５的倍数则喊：“Buzz”，如果３和５的公倍数则喊：“Fizz Buzz”

梳理一下，需求如下：

| 编号 | 需求 |
| --- | --- |
| １ | 正常情况下报数字 |
| ２ | ３的倍数时报“Fizz” |
| ３ | ５的倍数时报“Buzz” |
| ４ | ３和５的公倍数时报“Fizz Buzz” |

需求列清楚了，可以从测试开始，使用的是本地化的[cyber-dojo](http://www.cyber-dojo.org)。

### 需求１

``` python
def test_it_should_print_the_number_itself_if_it_is_a_normal_number(self):
        # arrange
        fizzbuzz = fizz_buzz_kata.FizzBuzz()
 
        # act
        # assert
        assert fizzbuzz.say(1) == "1"
        
```

为了让测试通过，有了如下的代码：

``` python
class FizzBuzz:    
    def say(self,number):
        return str(number)
```

### 需求2、３、４

``` python
    def test_it_should_print_fizz_if_it_is_a_number_times_of_three(self):
        fizz = fizz_buzz_kata.FizzBuzz()
        self.assertEqual(fizz.say(3),"fizz")
        self.assertEqual(fizz.say(6),"fizz")
 
    def test_it_should_print_buzz_if_it_is_a_number_times_of_five(self):
        buzz = fizz_buzz_kata.FizzBuzz()
        self.assertEqual(buzz.say(5),"buzz")
 
    def test_it_should_print_fizzbuzz_if_it_is_a_number_times_of_five_and_three(self):
        fizzbuzz = fizz_buzz_kata.FizzBuzz()
        self.assertEqual(fizzbuzz.say(15),"fizzbuzz")

```

实现：

``` python
class FizzBuzz:    
    def say(self,number):
        if number % 3 == 0 and number % 5 != 0:
            return "fizz"
        elif number % 5 == 0 and number % 3 != 0:
            return "buzz"
        elif number % 5 == 0 and number % 3 == 0:
            return "fizzbuzz"
        else:
            return str(number)

```


### 识别坏味道

最明显的坏味道就是say函数，典型的Golden function。
* 条件语句太多，但不能清晰地表明它的语义。
* return语句返回的内容和条件语句密切相关。
* 大量的 __if elif__ 使得让我感觉这里需要应用某个模式。

首先来解决第一个问题，通常的办法是把条件语句提取成命名函数，来起到强化语义的作用。

``` python
class FizzBuzz:    
    def say(self,number):
        if isFizz(number):
            return "fizz"
        elif isBuzz(number):
            return "buzz"
        elif isFizzBuzz(number):
            return "fizzbuzz"
        else:
            return str(number)

def isFizz(number):
    return number % 3 == 0 and number % 5 != 0

def isBuzz(number):
    return number % 5 == 0 and number % 3 != 0:

def isFizzBuzz(number):
    return number % 5 == 0 and number % 3 == 0

```

这样重构后，迅速跑通所有的测试，没有改变我们代码的外部行为，但条件语句的语义信息已经足够清晰了。
接下来是第二个坏味道，不难得出，条件语句与返回值是一一对应的，如果这样的好基友出现在代码里，我们首先考虑的是让他们在一起。

``` python

class FizzBuzz:    
    def __init__(self):
        self.handleFizz = HandleFizz()
        self.handleBuzz = HandleBuzz()
        self.handleFizzBuzz = HandleFizzBuzz()
        self.handleNormalNumber = HandleNumber()

    def say(self,number):
        if self.handleFizz.isFizz(number):
            return self.handlefizz.say(number)
        elif self.handleBuzz.isBuzz(number):
            return self.handleBuzz.say(number)
        elif self.handleFizzBuzz.isFizzBuzz(number):
            return self.handleFizzBuzz.say(number)
        else:
            return self.handleNormalNumber.say(number)


class HandleFizz:
   def isFizz(self, number):
       return number % 3 == 0 and number % 5 != 0

   def say(self, number):
       return "fizz"
 
 
class HandleBuzz:
    def isBuzz(self, number):
        return number % 5 == 0 and number % 3 != 0:

    def say(self, number):
        return "buzz"
 
 
class FizzBuzz:
    def isFizzBuzz(self, number):
        return number % 5 == 0 and number % 3 == 0

    def say(self,number):
        return "fizzbuzz"
 
 
class NormalNumber:
    def say(self, number):
        return str(number)

```

最后就只剩下这个长长的条件判断结构让我们不爽了。
过多的条件判断会让代码的语义难以理解，如此长的逻辑流通常也是容易出错的地方。我们需要梳理一下，套用某种模式来管理这样的复杂多。过多的条件判断一般会考虑两种模式：

* State
* Chain of Responsibility

　　前者的应用场景是长长的条件判断来控制对象的状态；而后者的应用场景是长长的条件语句来控制对同一数据的不同处理。显然，我们的场景需要职责链模型。

> 使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这个对象连成一条链，并沿着这条链传递该请求，直到一个对象处理它为止。

其实，经过上面的重构，此时的职责链已经呼之欲出了,直接给出代码。

``` python

class Fizzbuzz:
    def say(self, num):
        handleNormal = HandleNormal(None)
        handleBuzz = HandleBuzz(handleNormal)
        handleFizz = HandleFizz(handleBuzz)
        handleFizzBuzz = HandleFizzBuzz(handleFizz)
        result = handleFizzBuzz.say(num)
        return result
 
  
class Handler:
    def __init__(self, _successor):
        self.successor = _successor
 
    def doSuccessor(self, num):
        if self.successor is not None:
            return self.successor.say(num)
 
 
class HandleFizzBuzz(Handler):
    def isMyWay(self, num):
        return (num % 3 == 0) and (num % 5 ==0)
 
    def say(self, num):
        if self.isMyWay(num):
            return "fizzbuzz"
        return self.doSuccessor(num)
            
 
class HandleFizz(Handler):
    def isMyWay(self, num):
        return (num % 3 == 0)
 
    def say(self, num):
        if self.isMyWay(num):
            return "fizz"
        elif self.successor is not None:
            return self.successor.say(num)
 
 
class HandleBuzz(Handler):
    def isMyWay(self, num):
        return (num % 5 ==0)
 
    def say(self, num):
        if self.isMyWay(num):
            return "buzz"
        elif self.successor is not None:
            return self.successor.say(num)
 
 
class HandleNormal(Handler):
    def say(self, num):
        return str(num)
	

```


　　当然了，当场有小伙子们提出了可以将职责链处理里向下转移的部分提取到Handler类当中去，我觉得也可以，主要看应用场景。如果考虑到以后会有更复杂的转移条件，则目前这样的状态好一些；如果转移场景比较固定，进一步提取，也不失为一个好方法。