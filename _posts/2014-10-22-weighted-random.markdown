---
layout: post
title: "Python中的加权随机"
date: 2014-10-22 10:29:51
category: "Python提高班"
tags: python random
author: bingwen
---
我们平时比较多会遇到的一种情景是从一堆的数据中随机选择一个, 大多数我们使用`random`就够了, 但是假如我们要选取的这堆数据分别有自己的权重, 也就是他们被选择的概率是不一样的, 在这种情况下,  就需要使用`加权随机`来处理这些数据
<!--break-->

## 1. 简单线性方法

下面是一种简单的方案, 传入权重的列表(`weights`), 然后会返回随机结果的索引值(index), 比如我们传入`[2, 3, 5]`, 那么就会随机的返回0(概率0.2), 1(概率0.3), 2(概率0.5)

简单的思路就是把所有的权重加和, 然后随机一个数, 看看落在哪个区间

    import random

    def weighted_choice(weights):
        totals = []
        running_total = 0

        for w in weights:
            running_total += w
            totals.append(running_total)

        rnd = random.random() * running_total
        for i, total in enumerate(totals):
            if rnd < total:
                return i

## 2. 加速搜索

上面这个方法看起来非常简单, 已经可以完成我们所要的加权随机, 然是最后的这个`for`循环貌似有些啰嗦, `Python`有个内置方法`bisect`可以帮我们加速这一步

    import random
    import bisect

    def weighted_choice(weights):
        totals = []
        running_total = 0

        for w in weights:
            running_total += w
            totals.append(running_total)

        rnd = random.random() * running_total
        return bisect.bisect_right(totals, rnd)

`bisect`方法可以帮我们查找`rnd`在`totals`里面应该插入的位置, 两个方法看起来差不多, 但是第二个会更快一些, 取决于`weights`这个数组的长度, 如果长度大于1000, 大约会快30%左右

# 3. 去掉临时变量

其实在这个方法里面`totals`这个数组并不是必要的, 我们调整下策略, 就可以判断出`weights`中的位置

    def weighted_choice(weights):
      rnd = random.random() * sum(weights)
      for i, w in enumerate(weights):
          rnd -= w
          if rnd < 0:
              return i

这个方法比第二种方法竟然快了一倍, 当然, 从算法角度角度, 复杂度是一样的, 只不过我们把赋值临时变量的功夫省下来了, 其实如果传进来的`weights`是已经按照从大到小排序好的话, 速度会更快, 因为rnd递减的速度最快(先减去最大的数)

# 4. 更多的随机数

如果我们使用同一个权重数组`weights`, 但是要多次得到随机结果, 多次的调用`weighted_choice`方法, `totals`变量还是有必要的, 提前计算好它, 每次获取随机数的消耗会变得小很多

    class WeightedRandomGenerator(object):
        def __init__(self, weights):
            self.totals = []
            running_total = 0

            for w in weights:
                running_total += w
                self.totals.append(running_total)

        def next(self):
            rnd = random.random() * self.totals[-1]
            return bisect.bisect_right(self.totals, rnd)

        def __call__(self):
            return self.next()

在调用次数超过1000次的时候, `WeightedRandomGenerator`的速度是`weighted_choice`的100倍


所以我们在对同一组权重列表进行多次计算的时候选择方法4, 如果少于100次, 则使用方法3

# 5. 使用accumulate 

在`python3.2`之后, 提供了一个`itertools.accumulate`方法, 可以快速的给`weights`求累积和

    >>>> from itertools import accumulate
    >>>> data  = [2, 3, 5, 10]
    >>>> list(accumulate(data))
    [2, 5, 10, 20]

如果你有更好的方法, 欢迎在留言区讨论

参考文章: [Weighted random generation in Python](http://eli.thegreenplace.net/2010/01/22/weighted-random-generation-in-python/)
