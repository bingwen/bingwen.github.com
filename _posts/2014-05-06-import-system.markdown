---
layout: post
title: "模块的内置方法"
date: 2014-05-06 10:29:51
category: "Python提高班"
tags: python
author: bingwen
---
这期的话题有点简单, 整理下常见的模块内置方法

刚开始学Python的时候, 看到`if __name__ == '__main__': main()`就不明觉厉了, 一直好奇`__name__`这货到底是个什么东东

实际上, `__name__`是Python中模块的内置属性, 除此之外, 经常用到的还有`__file__`, `__doc__`, `__all__`, `__package__`等等
<!--break-->

## 1. `__name__`

`__name__`是模块内置的一个属性, 一般如果模块是被引用的时候, 它的值是模块名, 如果这个python文件被直接运行, 那么它的值是`'__main__'`

    >>> __name__
    '__main__'
    >>> import os
    >>> os.__name__
    'os'

所以我们写一个脚本的时候,通过判断`__name__`来确定脚本是被引用, 还是被直接运行

    if __name__ == `__main__`:
        main()

## 2. `__file__`

被引用模块文件的路径

    >>> import os
    >>> os.__file__
    '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/os.pyc'

## 3. `__doc__`

模块的注释文档

    class MyClass:
        """这是MyClass的注释, 
        调用下面myClass.__doc__的时候会返回这段内容"""

        def funcA(self):
            """这是funcA的注释文档"""
            return "hello"

    myClass = MyClass()

调用`myClass.__doc__`会返回`MyClass`的注释文档, `myClass.funcA.__doc__`会返回`funcA`的注释文档

如果执行`help(MyClass)`, 所看到的文档实际就是`DocStrings`收集该类下所有的注释生成的文档

## 4. `__all__`

当你写一个Python的模块的时候, 一般在`__init__.py`中指定`__all__`来表示当这个模块被`import * from xxx`的时候, 有哪些模块会被`import`进来, 

当然, 我们推荐尽量不使用`import *`


## 5. `__package__`

`__package__`主要是为了相对引用而设置的一个属性, 如果所在的文件是一个`package`的话, 它和`__name__`的值是一样的, 如果是子模块的话, 它的值就跟父模块一致

比如 `modA/modB/aa.py`中`__name__`的值是`modA.modB.aa` `__package__`是`modA.modB`
`modA/modB/__init__.py`中`__name__`和`__package__`的值都是`modA.modB`

参考文章: [The import system](https://docs.python.org/3/reference/import.html)
