---
layout: post
title: "Google Objective-C Style Guide (2) 空格空行和格式"
date: 2013-09-11 10:29:51
category: "Google-Objective-C-Style-Guide"
tags: google obj-c
author: bingwen
---

##空格空行和格式

###缩进

请使用空格（而不是tab）来缩进，缩进使用2个空格，如果使用tab，请配置好你的编辑器

###代码长度

一行代码最多80个字符,你可以在你的.vimrc里面设置下面的命令
    set cc=80
这样在vim中第80个字符的位置为一条红线来提醒你每次编码不超过这个数字
<!--break-->

###方法的声明和定义

在-、+和返回类型之间应该有一个空格，参数列表之前没有空格，但是参数之间是需要空格的（换行最好），如下代码

一般的方法声明：

    - (void)doSomethingWithString:(NSString *)theString {
      ...
    }

*号之前空格是可有可无的

如果有许多的参数，能放到一行就放到一行，如果一行里面放不下，那么换行的时候应该使用冒号对齐的方式，如下

    - (void)doSomethingWith:(GTMFoo *)theFoo
                       rect:(NSRect)theRect
                   interval:(float)theInterval {
      ...
    }

如果第一个参数太他妈的短了，后面的参数又太长根本没法对齐呢？那第二个参数之前至少给4个空格就好了，后面仍坚持冒号对齐原则，如下

    - (void)short:(GTMFoo *)theFoo
              longKeyword:(NSRect)theRect
        evenLongerKeyword:(float)theInterval
                    error:(NSError **)theError {
      ...
    }

###方法调用

方法调用应该基本上和方法声明差不多

所有的参数在同一行

    [myObject doFooWith:arg1 name:arg2 error:arg3];

或者每个参数各占一行

    [myObject doFooWith:arg1
                   name:arg2
                  error:arg3];

请**别**像下面这样

    [myObject doFooWith:arg1 name:arg2  // 多于一个参数
                  error:arg3];
    
    [myObject doFooWith:arg1
                   name:arg2 error:arg3];
    
    [myObject doFooWith:arg1
              name:arg2  // 用单词对齐，应该使用冒号对齐
              error:arg3];

###@public和@private

@public和@private之前的缩进只要一个空格，基本上和C++中差不多

    @interface MyClass : NSObject {
     @public
      ...
     @private
      ...
    }
    @end

###异常

异常用到的@try、@catch、@finally应该单独一行，并且和大括号之间空格一个

如果你一定要抛异常，请按照下面的格式，当然，你可以看这里[为什么避免抛异常](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml#Avoid_Throwing_Exceptions)

    @try {
      foo();
    }
    @catch (NSException *ex) {
      bar(ex);
    }
    @finally {
      baz();
    }

###Protocols

protocol使用的时候在id和protocol之间不要空格，如下

    @interface MyProtocoledClass : NSObject<NSWindowDelegate> {
     @private
      id<MyFancyDelegate> _delegate;
    }
    - (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
    @end

###Blocks

创建block的时候使用目标选择器模式，这样更容易阅读。block里面的代码缩进4个空格。

这里有几个规则，取决于这个block有多长：

- 如果一行可以放下，就不要嵌套了
- 如果必须嵌套，那么后面的大括号必须和声明起始的第一行的第一个字符对齐
- block内的代码必须缩进4个空格
- 如果一个block太他妈的长了，应该移除来，赋值给一个临时变量
- 如果block没有参数，'^'和'{'之间没有空格。如果有参数，'^'和'('之间没有空格，但是')'和'{'之间有空格

    // 一行可以放得下的情况
    [operation setCompletionBlock:^{ [self onOperationDone]; }];
    
    // 分行的，内部空4格，结束的}和第一行最左边对齐 
    [operation setCompletionBlock:^{
        [self.delegate newDataAvailable];
    }];
    
    // 使用C的API的时候，仍然是obc的对齐风格
    dispatch_async(_fileIOQueue, ^{
        NSString* path = [self sessionFilePath];
        if (path) {
          // ...
        }
    });
    
    // 有参数的情况，主意空格 |^(SessionWindow *window) {|
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];
    
    // 如果参数在一行放不下，换行，空格使用照旧 
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:
            ^(SessionWindow *window) {
                if (window) {
                  [self windowDidLoad:window];
                } else {
                  [self errorLoadingWindow];
                }
            }];
    
    // block太长，声明一个临时变量 
    void (^largeBlock)(void) = ^{
        // ...
    };
    [_operationQueue addOperationWithBlock:largeBlock];
    
    // 一个方法里面多个block使用 
    [myObject doSomethingWith:arg1
        firstBlock:^(Foo *a) {
            // ...
        }
        secondBlock:^(Bar *b) {
            // ...
        }]; 

###容器和字符串

Xcode 4.4之后的项目，都推荐使用容器（array和dictionary）

如果一行可以放得下，在开始和结束的括号之间要有空格，如下

    NSArray* array = @[ [foo description], @"Another String", [bar description] ];
    
    NSDictionary* dict = @{ NSForegroundColorAttributeName : [NSColor redColor] };

而不是

    NSArray* array = @[[foo description], [bar description]];
    
    NSDictionary* dict = @{NSForegroundColorAttributeName: [NSColor redColor]};

如果一行里面放不下，那么分多行，内部需要空2格

这时候的dictionary里面冒号前后都要一个空格

    NSArray* array = @[
      @"This",
      @"is",
      @"an",
      @"array"
    ];
    
    NSDictionary* dictionary = @{
      NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
      NSForegroundColorAttributeName : fontColor
    };
